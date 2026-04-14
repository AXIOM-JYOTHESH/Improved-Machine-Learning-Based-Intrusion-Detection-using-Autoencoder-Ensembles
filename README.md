# Kitsune-py-Colab

This repository is a Google Colab friendly version of `Kitsune-py`.

It keeps the original Kitsune Python implementation and project files, but the workflow is updated so the repository can be pushed to GitHub and then cloned directly inside Colab. That means we no longer depend on uploading a zip of the project into the Colab session.

Based on:

*Yisroel Mirsky, Tomer Doitshman, Yuval Elovici, and Asaf Shabtai, "Kitsune: An Ensemble of Autoencoders for Online Network Intrusion Detection", Network and Distributed System Security Symposium 2018 (NDSS'18)*

## What changed

- The full original `Kitsune-py` project is included here.
- A Colab notebook is provided for a `git clone` based workflow.
- This folder is intended to be uploaded as its own GitHub repository and used directly from Google Colab.

## Colab workflow

Use [kitsune_colab_clone_demo.ipynb](./kitsune_colab_clone_demo.ipynb).

The notebook:

- installs `tshark` and Python dependencies
- clones this repository from GitHub into `/content`
- mounts Google Drive
- loads a `.pcap`, `.pcapng`, or `.tsv` capture
- runs Kitsune and plots RMSE anomaly scores

## How to use in Google Colab

1. Push this folder to a GitHub repository.
2. Open `kitsune_colab_clone_demo.ipynb` in Google Colab.
3. Set `REPO_URL` to your GitHub repository URL.
4. Set `capture_path` to your dataset location in Google Drive.
5. Run the notebook cells in order.

## Original usage

You can still use the project like the original `Kitsune-py` implementation:

```python
from Kitsune import Kitsune
import numpy as np

maxAE = 10
FMgrace = 5000
ADgrace = 50000
packet_limit = np.inf
path = "captured.pcap"

K = Kitsune(path, packet_limit, maxAE, FMgrace, ADgrace)
```

Process packets one by one:

```python
while True:
    rmse = K.proc_next_packet()
    if rmse == -1:
        break
    print(rmse)
```

## Notes

- `tshark` is preferred for parsing pcaps when available.
- If `tshark` is not available, the code falls back to `scapy`.
- After the first run, a generated `.tsv` can be reused for faster later runs.

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE).
