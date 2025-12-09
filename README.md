# Python NoGIL Docker Images

Docker images with GIL (Global Interpreter Lock) disabled, based on the official Python images.  
**This image was created for my own experiments.**

- [Official Image](https://hub.docker.com/_/python)
- [Official Image GitHub Repository](https://github.com/docker-library/python)
- [Docker Hub for this image](https://hub.docker.com/r/tdciwasaki/python-nogil)

## 🚀 Quick Start

```bash
docker pull tdciwasaki/python-nogil:latest
docker run -it tdciwasaki/python-nogil:latest python
```

## 📦 Available Tags

| Tag | Python Version | Base Image | Platform |
|------|------------------|---------------|-----------------|
| `latest`, `3.14`, `3.14-slim-trixie` | 3.14.0 | Debian Trixie Slim | amd64, arm64 |

### Tag Descriptions

- `latest`: Latest stable version (always the latest build from the `main` branch)
- `3.14`: Latest version of Python 3.14 series
- `3.14.x`: Specify Python version (e.g., `3.14.2`)
- `3.14-slim-trixie`: Combination of Python 3.14 + Debian Trixie Slim

## ✅ Verifying GIL Disabling

```bash
docker run --rm tdciwasaki/python-nogil:latest python -c "
import sys
print(f'Python {sys.version}')
print(f'GIL disabled: {not sys.flags.gil}')
"
```

Output Example:
```
Python 3.14.0 (main, Oct 10 2025, 00:00:00) [GCC 12.2.0]
GIL disabled: True
```

## 🛠️ Usage Examples

### Using in Dockerfile

```dockerfile
FROM tdciwasaki/python-nogil:3.14

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python", "app.py"]
```

### Multi-threaded Application

```python
import threading
import time

def worker(n):
    """CPU-intensive task (benefits from GIL disabling)"""
    total = 0
    for i in range(10_000_000):
        total += i * n
    return total

threads = []
for i in range(4):
    t = threading.Thread(target=worker, args=(i,))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

print("✓ All threads completed")
```

### Enabling GIL

```shell
# Run with GIL disabled
python app.py
# Run with GIL enabled
PYTHON_GIL=1 python app.py
```

## 🌐 Documentation

- [日本語 (Japanese)](./README_ja.md)
