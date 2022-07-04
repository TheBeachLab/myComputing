# Metal MPS support in PyTorch

More info [here](https://pytorch.org/blog/introducing-accelerated-pytorch-training-on-mac/). At the time of writing I am running this in macOS Ventura Beta 2 on a M1 Pro processor.

## Install PyTorch

```bash
pip3 install --pre torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/nightly/cpu
pip3 install torch torchvision
``` 

## Test

```python
import torch
x = torch.rand(5, 3)
print(x)
``` 

Expected output similar to

```python
tensor([[0.3380, 0.3845, 0.3217],
        [0.8337, 0.9050, 0.2650],
        [0.2979, 0.7141, 0.9069],
        [0.1449, 0.1132, 0.1375],
        [0.4675, 0.3947, 0.1426]])
```

Test the mps support

```python
torch.has_mps
True
```
