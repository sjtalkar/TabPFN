# TabPFN

[For Time Series watch] (https://www.youtube.com/watch?v=8RbxKffv_BA)

Running in Databricks TABPFN
Yes, TabPFN can run directly inside Databricks. You can run it locally on your cluster's compute so your data never leaves your workspace, or connect to Prior Labs' hosted API if you do not have GPU clusters available. [1] (https://docs.priorlabs.ai/quickstart), [2] (https://priorlabs.ai/blog-posts/tabpfn-on-databricks), [3] (https://rehoyt.medium.com/tabpfn-v2-5-an-even-better-algorithm-68b72c6be5d5)

1. Choose Your Setup & Installation

Depending on your cluster hardware and data governance needs, install one of two packages in your Databricks notebook or cluster libraries:
• Option A: Local Cluster Execution (Data stays in Databricks)
Run %pip install tabpfn inside a notebook cell (or add tabpfn to your cluster's PyPI libraries). This runs the model weights directly on your Databricks driver or worker nodes using PyTorch. [1] (https://github.com/PriorLabs/tabpfn), [2] (https://pypi.org/project/tabpfn/2.0.0/), [3] (https://rehoyt.medium.com/tabpfn-v2-5-an-even-better-algorithm-68b72c6be5d5), [4] (https://huggingface.co/Prior-Labs/TabPFN-v2-clf)
• Option B: Hosted API Client (No GPU cluster needed)
Run %pip install tabpfn-client to offload inference to Prior Labs' managed GPUs. Note that this sends your tabular dataset outside Databricks to the Prior Labs API. [1] (https://docs.priorlabs.ai/cookbook/tabpfn_35_vs_baselines), [2] (https://rehoyt.medium.com/tabpfn-v2-5-an-even-better-algorithm-68b72c6be5d5), [3] (https://docs.priorlabs.ai/quickstart)

2. Cluster & Hardware Requirements

• Runtime: Use a standard Databricks Runtime for Machine Learning (DBR ML), which already includes Python 3.10+, PyTorch, and scikit-learn. [1] (https://huggingface.co/Prior-Labs/TabPFN-v2-clf), [2] (https://github.com/PriorLabs/tabpfn)
• Compute: A GPU-enabled cluster (such as an NVIDIA T4 or A10G node with at least 8GB–16GB VRAM) is strongly recommended for local execution. Running locally on a CPU cluster works, but it is only practical for small datasets (typically under 1,000 to 5,000 rows). [1] (https://github.com/PriorLabs/tabpfn), [2] (https://docs.priorlabs.ai/quickstart)

3. Authentication & Token Setup

Before running inference, you must accept the model license on Prior Labs (or Hugging Face for older v2 weights) and generate an access token. [1] (https://docs.priorlabs.ai/cookbook/quickstart), [2] (https://huggingface.co/Prior-Labs/TabPFN-v2-clf), [3] (https://docs.priorlabs.ai/quickstart)
• Store via Databricks Secrets: Save your token securely using the Databricks CLI or Secrets API rather than hardcoding it in notebooks:bash
databricks secrets create-scope tabpfn
databricks secrets put-secret tabpfn token
Use code with caution.
• Load in your Notebook: Retrieve the secret and set it as an environment variable before initializing TabPFNClassifier or TabPFNRegressor: [1] (https://github.com/databricks-industry-solutions/tabpfn-databricks), [2] (https://docs.priorlabs.ai/quickstart), [3] (https://docs.priorlabs.ai/cookbook/quickstart)
python
import os
os.environ["TABPFN_TOKEN"] = dbutils.secrets.get(scope="tabpfn", key="token")
Use code with caution.

4. Databricks Integration Resources

• Official Solution Accelerator: Databricks maintains a TabPFN on Databricks GitHub repository with ready-to-use notebooks for classification, regression, outlier detection, and time-series forecasting.
• Governance & Serving: Because TabPFNClassifier and TabPFNRegressor follow the standard scikit-learn API, you can log them with MLflow, register them in Unity Catalog, and deploy them to Mosaic AI Model Serving endpoints. [1] (https://www.youtube.com/watch?v=eZvY49qB4yY), [2] (https://github.com/databricks-industry-solutions/tabpfn-databricks), [3] (https://priorlabs.ai/blog-posts/tabpfn-on-databricks), [4] 


(https://pypi.org/project/tabpfn/2.0.0/), [5] (https://docs.priorlabs.ai/quickstart)


Yes, TabPFN can perform the same core time-series forecasting tasks as Prophet using its official time-series extension, TabPFN-TS (pip install tabpfn-time-series). [1] (https://github.com/PriorLabs/tabpfn-time-series)
It works by breaking timestamps into calendar and seasonal features—such as day of the week, month, and cyclic frequencies—and solving the forecast as a zero-shot tabular regression problem without requiring model training or hyperparameter tuning. [1] (https://arxiv.org/html/2501.02945v4), [2] (https://docs.priorlabs.ai/capabilities/forecasting), [3] (https://medium.com/@ShubhamKothiya/tabpfn-ts-how-a-tabular-foundation-model-outperforms-specialized-time-series-forecasting-models-e06533e059f5)

TabPFN-TS vs. Prophet


Capability	TabPFN-TS	Meta Prophet
Setup & Training	Zero-shot inference in a single forward pass with no fitting required.	Fits an additive/multiplicative curve model to your historical series.
Seasonality & Calendars	Automatically extracts cyclic calendar features and dominant seasonal frequencies.	Excels at daily, weekly, and yearly seasonality plus built-in country holidays.
External Regressors	Natively supports known future covariates and multiple interacting variables.	Supports extra regressors, though complex non-linear interactions require manual engineering.
Uncertainty Intervals	Outputs full probabilistic quantile forecasts and calibrated confidence intervals.	Generates uncertainty intervals via Monte Carlo simulation.
Trend Extrapolation	Weaker on long-term linear or exponential growth trends outside historical ranges.	Stronger at modeling long-term growth curves and automatic trend changepoints.
Interpretability	Black-box transformer predictions (requires post-hoc feature attribution).	Highly transparent decomposition into explicit trend, holiday, and seasonal components.


When to Choose Which

• Use TabPFN-TS when your forecast depends heavily on external variables (such as promotions, weather, or pricing), complex non-linear calendar patterns, or missing historical data. [1] (https://github.com/PriorLabs/tabpfn-time-series), [2] (https://docs.priorlabs.ai/capabilities/forecasting)
• Stick with Prophet when you have strong long-term growth trends, sharp structural changepoints, or need stakeholders to inspect separate trend and holiday curves. [1] (https://www.youtube.com/watch?v=8RbxKffv_BA), [2] (https://arxiv.org/html/2501.02945v4)


• Roughly how many rows and columns are in your Delta tables?
I can share a tailored Databricks notebook snippet for your setup.
