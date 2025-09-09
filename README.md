
# GNN EV Route Optimization (Traffic + Signals)

End-to-end pipeline that pulls **live traffic & signal data**, turns it into a **road graph**, learns **edge travel times** with a **Graph Neural Network**, and computes **EV-feasible routes** with battery constraints.

> Research/education only. Not a navigation product.

## Features
- **APIs ➜ CSV**: traffic speeds/TT + signal timing, cleaned & time-aligned
- **Graph build**: NetworkX directed multigraph with rich edge/node attrs
- **GNN**: spatio-temporal encoder (PyTorch Geometric) for ETA/stop-prob
- **Routing**: learned-cost **A\*** + **resource-constrained** path (SoC/charging)
- **Serve**: FastAPI `/route` (source, dest, SoC)

## Quickstart
```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 1) Fetch & fuse (APIs ➜ CSV)
python scripts/fetch_apis.py --traffic-key ... --signals-key ... --out data/raw
python scripts/fuse_to_csv.py --in data/raw --out data/interim

# 2) Build graph & windows
python scripts/build_graph.py --in data/interim --out data/processed

# 3) Train GNN & evaluate
python scripts/train_gnn.py --config configs/train.yaml --out artifacts/gnn.pt
python scripts/eval.py --model artifacts/gnn.pt --out artifacts/metrics.json

# 4) Route (EV constraints)
python scripts/route.py --src "lat,lon" --dst "lat,lon" --soc0 0.8 --socmin 0.1 --time "2025-09-09T14:00:00Z"
