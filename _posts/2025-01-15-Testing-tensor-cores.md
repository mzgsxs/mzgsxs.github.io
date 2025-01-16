# Testing-tensor-core
Altough tensor-core was introduced in the RTX20s series, I have only tried it very breifly after the launch but never used it in practice apart from deloying tensorRT models. It only supports FP16 initially but it's now supporting more data types.
With siginificant boost of tensor cores in RTX50s series, and disappointing increase in its shader cores performance, it's worth spending some time investigating this feature. 
I want to test if the performance of the tensor cores are comparable to odinary cuda/shader cores and wether I should upgrade to RTX5090.

I'm runing all tests on my RTX3080ti, which have 320 tensor cores and [offers 136 dense and 273 sparse FP16 TFLOPS](https://en.wikipedia.org/wiki/GeForce_30_series). 
For higher precision, tensor core typically use TF32 data type, it only have 10 bits for the significand (reduced from FP32's 23 bits), this reduction in significand bits lowers precision to about 3 decimal digits, 
but with siginificant speedup (500 TFLOPS compares to 60 TFLOPS	for TF32 with Nvidia H100). Server GPUs have a better support for [higher precision tensorcore computations](https://www.anandtech.com/show/17327/nvidia-hopper-gpu-architecture-and-h100-accelerator-announced).

Does my RTX3080ti support TF32? It does according to [this](https://www.nvidia.com/content/PDF/nvidia-ampere-ga-102-gpu-architecture-whitepaper-v2.1.pdf), 
but is this performance a result from the regular shader cores? I need test it to find out, Monitor it's utilization requires more effort than `nvidia-smi`.


## Monitoring
The easiest way is to compare the numerical result:
```python
import torch
import torch.profiler

matrix_size = 2<<12  # Large enough to utilize Tensor Cores optimally

A = torch.randn((matrix_size, matrix_size), device='cuda', dtype=torch.float32)
B = torch.randn((matrix_size, matrix_size), device='cuda', dtype=torch.float32)

def test():
    with torch.profiler.profile(
        activities=[torch.profiler.ProfilerActivity.CPU, torch.profiler.ProfilerActivity.CUDA],
        on_trace_ready=torch.profiler.tensorboard_trace_handler('./log'),
        record_shapes=True,
        with_stack=True
    ) as prof:
        # Run your training code here
        # Generate two large random matrices on the GPU with FP32 precision
        C_old = torch.zeros((matrix_size, matrix_size), device='cuda', dtype=torch.float32)
        for _ in range(100):
            C = torch.matmul(A, B)  # Matrix multiplication using TF32 on Tensor Cores

    print(prof.key_averages().table(sort_by="cuda_time_total"))
    return C


torch.backends.cuda.matmul.allow_tf32 = True  # Enable TF32 for matmul
torch.backends.cudnn.allow_tf32 = True       # Enable TF32 for cuDNN operations
C_tf32 = test()

torch.backends.cuda.matmul.allow_tf32 = False  # Enable TF32 for matmul
torch.backends.cudnn.allow_tf32 = False       # Enable TF32 for cuDNN operations
C_fp32 = test()

error = (C_tf32 - C_fp32).abs().max()
print(f"Max absolute error between TF32 and FP32: {error}")

```

## Automatic Mixed Precision package
This is the easiest way to leverage tensor core, to allow some ops to [automatically run in lower precision](https://pytorch.org/docs/stable/amp.html).
I will use a standard [tutorial](https://pytorch.org/tutorials/beginner/basics/quickstart_tutorial.html) for testing. 

Include this to import `amp`
```python
from torch.amp import autocast, GradScaler
```
And modify the following inference and backprop code
```
scaler = GradScaler('cuda')
...
    with autocast('cuda'):  # Enable mixed precision
        pred = model(data)
        loss = loss_fn(pred, y)

    # Backpropagation
    scaler.scale(loss).backward()  # Scale the loss for stability
    scaler.step(optimizer)
    scaler.update()
...
```