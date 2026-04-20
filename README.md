# Branch Prediction with Deep Learning

This repository contains a notebook-first deep learning project for CPU branch prediction.
The main artifact is [DL_final_project.ipynb](DL_final_project.ipynb), which builds and evaluates
multiple predictors, compares their accuracy and latency, and visualizes the trade-offs between
raw prediction quality and compute cost.

## Project Idea

Branch prediction is one of the key bottlenecks in modern processors. When the predictor is wrong,
the pipeline loses time recovering from the misprediction. This project studies whether a small
neural predictor can provide a strong speed/accuracy balance, and whether a hybrid design can do
better by sending difficult cases to a heavier model.

The notebook evaluates three predictor roles:

- FAST: a lightweight MLP-style predictor optimized for low latency.
- SLOW: a transformer-based specialist that is more expressive but more expensive.
- HYBRID: an adaptive mixture-of-experts design that uses FAST for easy cases and routes uncertain
	or hard cases to SLOW.

The hybrid router combines confidence gating with per-PC history so that the transformer is used
only when it is likely to add value.

## Experimental Setup

The experiments are organized around benchmark traces from SPEC CPU workloads. The report figures
focus on the following workloads:

- bzip2
- mcf
- gobmk

The notebook reports:

- overall accuracy comparisons,
- branch misprediction rates,
- per-benchmark accuracy gains,
- training curves for FAST and SLOW,
- routing confidence distributions,
- and inference latency.

## Main Results

The results in the report show a clear speed/accuracy trade-off:

- FAST reaches 93.91% test accuracy with an inference latency of 11.71 ms per batch.
- SLOW reaches 80.45% best validation accuracy, but has the highest latency at 21.15 ms per batch.
- HYBRID reaches about 95.4% accuracy while using SLOW only 16.7% of the time, with a latency of
	17.03 ms per batch.
- The routing policy sends 83.3% of instances to FAST and 16.7% to SLOW at a confidence threshold
	of 0.35.

On the benchmark breakdown, HYBRID improves over FAST on all shown workloads:

- bzip2: 96.55% vs 96.12%
- mcf: 97.41% vs 97.16%
- gobmk: 92.51% vs 88.94%

The largest gain is on gobmk, where the adaptive router adds more than 3.5 percentage points.

## Figures

The following figures in [Images/](Images/) summarize the report:

| Figure | Description |
| --- | --- |
| [accuracy gains.png](Images/accuracy%20gains.png) | Workload-aware accuracy comparison between FAST and HYBRID |
| [latency.png](Images/latency.png) | Inference latency comparison for FAST, HYBRID, and SLOW |
| [confidence gating mechanism.png](Images/confidence%20gating%20mechanism.png) | Routing confidence distribution and thresholding logic |
| [fast learning curve.png](Images/fast%20learning%20curve.png) | FAST training and validation curve |
| [slow learning curve.png](Images/slow%20learning%20curve.png) | SLOW specialist training curve |
| [other graphs.png](Images/other%20graphs.png) | Combined summary plots from the report |

## Repository Structure

- [DL_final_project.ipynb](DL_final_project.ipynb): main notebook with training, evaluation, and plots.
- [Images/](Images/): exported figures used in the report and README.
- [README.md](README.md): project summary and experiment overview.

## How To Use

1. Open [DL_final_project.ipynb](DL_final_project.ipynb) in VS Code or Jupyter.
2. Run the notebook cells in order.
3. Review the generated plots and metrics in the notebook output.

If you want to compare the written report against the figures, start with the accuracy, latency,
and confidence-routing plots listed above.