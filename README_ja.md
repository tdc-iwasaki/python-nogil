# Python NoGIL Docker Images

Python 公式イメージをもとに GIL (Global Interpreter Lock) を無効化したイメージです。  
**このイメージは自身の実験のために作成したものです。**  

- [公式イメージ](https://hub.docker.com/_/python)
- [公式イメージの GitHub リポジトリ](https://github.com/docker-library/python)
- [このイメージの Docker Hub](https://hub.docker.com/r/tdciwasaki/python-nogil)

## 🚀 クイックスタート

```bash
docker pull tdciwasaki/python-nogil:latest
docker run -it tdciwasaki/python-nogil:latest python
```

## 📦 利用可能なタグ

| タグ | Python バージョン | ベースイメージ | プラットフォーム |
|------|------------------|---------------|-----------------|
| `latest`, `3.14`, `3.14-slim-trixie` | 3.14.2 | Debian Trixie Slim | amd64, arm64 |

### タグの説明

- `latest`: 最新の安定版（常に `main` ブランチの最新ビルド）
- `3.14`: Python 3.14 系の最新版
- `3.14.x`: Python バージョンを指定（例: `3.14.2`）
- `3.14-slim-trixie`: Python 3.14 + Debian Trixie Slim の組み合わせ

## ✅ GIL 無効化の確認

```bash
docker run --rm tdciwasaki/python-nogil:latest python -c "
import sys
print(f'Python {sys.version}')
print(f'GIL disabled: {not sys.flags.gil}')
"
```

出力例:
```
Python 3.14.0 (main, Oct 10 2025, 00:00:00) [GCC 12.2.0]
GIL disabled: True
```

## 🛠️ 使用例

### Dockerfile での使用

```dockerfile
FROM tdciwasaki/python-nogil:3.14

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python", "app.py"]
```

### マルチスレッドアプリケーション

```python
import threading
import time

def worker(n):
    """CPU集約的な処理（GIL無効化の恩恵を受ける）"""
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

### GIL の有効化

```shell
# GIL 無効で実行
python app.py
# GIL 有効で実行
PYTHON_GIL=1 python app.py
```
