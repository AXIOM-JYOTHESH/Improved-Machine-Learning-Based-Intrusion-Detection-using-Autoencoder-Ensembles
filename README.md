# Kitsune-py-Colab

This repository is a Google Colab friendly version of `Kitsune-py`.

It keeps the original Kitsune Python implementation and project files, but the workflow is updated so the repository can be pushed to GitHub and then cloned directly inside Colab. That means we no longer depend on uploading a zip of the project into the Colab session.

Based on:

*Yisroel Mirsky, Tomer Doitshman, Yuval Elovici, and Asaf Shabtai, "Kitsune: An Ensemble of Autoencoders for Online Network Intrusion Detection", Network and Distributed System Security Symposium 2018 (NDSS'18)*

## What changed

- The full original `Kitsune-py` project is included here.
- A Colab notebook is provided for a `git clone` based workflow.
- This folder is intended to be uploaded as its own GitHub repository and used directly from Google Colab.
- Large sample captures and the `Datsets` folder were removed to keep the repository smaller and easier to clone.

## Core files and why they matter

- `final.ipynb`: The main notebook interface for this project. Users run this notebook in Colab, upload a `.pcap` or `.pcapng` file, and view the anomaly score output that helps show when suspicious traffic happened.
- `Kitsune.py`: The top-level pipeline wrapper. It connects packet feature extraction to the KitNET anomaly detector and exposes the `proc_next_packet()` workflow used by the notebook.
- `FeatureExtractor.py`: Converts raw traffic captures into numerical feature vectors. It reads `.pcap`, `.pcapng`, or `.tsv` files and uses `tshark` or `scapy` to extract packet-level information.
- `netStat.py`: Builds the behavioral network statistics for each packet. These statistics capture communication patterns such as host-host activity, MAC-IP relationships, and timing behavior.
- `AfterImage.py`: Maintains the incremental statistics used by `netStat.py`. This is the feature-engineering engine that updates traffic behavior summaries as packets arrive.
- `KitNET/KitNET.py`: Implements the main KitNET anomaly detection logic. It learns normal behavior during the grace periods and later assigns anomaly scores to incoming traffic.
- `KitNET/dA.py`: Contains the autoencoder model used inside KitNET. These autoencoders measure reconstruction error, which becomes the basis for anomaly scoring.
- `KitNET/corClust.py`: Learns how input features should be grouped before training the ensemble of autoencoders. This is important for building the KitNET architecture automatically.
- `README.md`: Explains how to run the project and how the notebook-based workflow improves usability compared with the older setup.

Together, these files form the complete path from uploaded network capture to anomaly visualization: capture parsing, feature extraction, network-statistics generation, KitNET scoring, and final plotting in the notebook.

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
2. Open `final.ipynb` in Google Colab.
3. Upload your `.pcap`, `.pcapng`, or pre-generated `.tsv` capture to the Colab session.
4. Update the repository URL cell if you are using your own GitHub fork.
5. Change `capture_path` so it points to the uploaded file.
6. Run the cells from top to bottom without skipping order.

## What each notebook cell does

- Cell 1 installs `tshark` and Python packages. This matters because Kitsune needs packet parsing support before anything else can run.
- Cell 2 clones the repository into Colab and switches into the project folder. This makes sure the notebook uses the exact project code stored on GitHub.
- Cell 3 imports dependencies and adds the repo to Python's path. This prepares the Colab runtime to load Mirsky's Kitsune implementation.
- Cell 4 creates the `Kitsune` detector and sets the core parameters. This is the point where your uploaded capture is connected to the detection pipeline.
- Cell 5 times a short packet run. This gives a rough estimate of processing speed before you launch a full analysis.
- Cell 6 processes the full capture and stores RMSE anomaly scores. This is the main detection stage where suspicious traffic patterns are identified.
- Cell 7 plots the RMSE scores. This helps you visually see when attacks or abnormal behavior happened in the uploaded traffic.

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
- The old `mirai` sample files are not required by the Kitsune code itself. They were only used by the original demo script.

## License

This project is licensed under the MIT License. See [LICENSE](./LICENSE).
