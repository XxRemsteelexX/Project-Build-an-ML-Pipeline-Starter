# NYC Airbnb Price Prediction ML Pipeline

This project implements an end-to-end machine learning pipeline for predicting short-term rental prices in NYC using Airbnb data. The pipeline is designed for production use with automated retraining capabilities and comprehensive MLOps practices.

## Project Overview

This ML pipeline predicts typical rental prices for properties based on similar listings. It's built to handle weekly data updates and model retraining, making it suitable for property management companies operating in the short-term rental market.

## Key Features

- **End-to-End Pipeline**: Complete workflow from data ingestion to model deployment
- **MLOps Integration**: Uses MLflow for experiment tracking and model management
- **Automated Workflows**: Configurable pipeline steps with parameter management via Hydra
- **Data Validation**: Built-in data quality checks and monitoring
- **Model Versioning**: Comprehensive tracking of model versions and performance metrics
- **Production Ready**: Containerized components with proper dependency management

## Architecture

The pipeline consists of several modular components:

1. **Data Download** (`download`): Fetches the latest Airbnb dataset
2. **Data Cleaning** (`basic_cleaning`): Handles missing values, outliers, and data formatting
3. **Data Validation** (`data_check`): Performs statistical validation and drift detection
4. **Data Splitting** (`data_split`): Creates train/validation/test splits with stratification
5. **Model Training** (`train_random_forest`): Trains Random Forest regression model
6. **Model Testing** (`test_regression_model`): Validates model performance on test set

## Technology Stack

- **ML Framework**: scikit-learn for machine learning models
- **Experiment Tracking**: Weights & Biases (wandb) for experiment management
- **Workflow Orchestration**: MLflow for pipeline orchestration
- **Configuration Management**: Hydra for parameter configuration
- **Environment Management**: Conda for reproducible environments
- **Data Processing**: Pandas, NumPy for data manipulation

## Quick Start

### Prerequisites

- Python 3.8+
- Conda package manager
- Weights & Biases account

### Installation

1. Clone the repository:
```bash
git clone https://github.com/XxRemsteelexX/NYC-Airbnb-ML-Pipeline.git
cd NYC-Airbnb-ML-Pipeline
```

2. Create and activate the conda environment:
```bash
conda env create -f environment.yml
conda activate nyc_airbnb_dev
```

3. Configure Weights & Biases:
```bash
wandb login [your_api_key]
```

### Running the Pipeline

**Full Pipeline:**
```bash
mlflow run .
```

**Individual Steps:**
```bash
# Run specific steps
mlflow run . -P steps=download,basic_cleaning

# Run with custom parameters
mlflow run . -P steps=train_random_forest -P hydra_options="modeling.random_forest.n_estimators=100"
```

## Configuration

Pipeline behavior is controlled via `config.yaml`:

```yaml
main:
  project_name: nyc_airbnb
  experiment_name: development
  components_repository: "https://github.com/XxRemsteelexX/NYC-Airbnb-ML-Pipeline.git#components"

etl:
  sample: "sample1.csv"
  min_price: 10
  max_price: 350

modeling:
  test_size: 0.2
  val_size: 0.2
  random_seed: 42
  stratify_by: "neighbourhood_group"
  random_forest:
    n_estimators: 200
    max_depth: 50
    min_samples_split: 4
```

## Pipeline Components

### Data Processing
- **Price Filtering**: Removes unrealistic price outliers
- **Feature Engineering**: Creates TF-IDF features from listing descriptions
- **Geographic Stratification**: Ensures balanced representation across NYC boroughs

### Model Training
- **Algorithm**: Random Forest Regression optimized for rental price prediction
- **Features**: Property characteristics, location data, amenities, and text features
- **Validation**: Comprehensive cross-validation with multiple metrics

### Model Evaluation
- **Metrics**: MAE, RMSE, R² score for regression performance
- **Validation**: Out-of-bag scoring and holdout test evaluation
- **Monitoring**: Automated performance tracking and alerts

## Data Pipeline

The pipeline processes NYC Airbnb listing data with the following workflow:

1. **Raw Data Ingestion**: Downloads latest dataset from configured source
2. **Data Cleaning**: Removes invalid entries, handles missing values
3. **Feature Extraction**: Creates engineered features including:
   - Geographic features (borough, neighborhood)
   - Text features from listing descriptions
   - Property characteristics
   - Host information
4. **Data Validation**: Statistical tests for data quality and distribution shifts
5. **Train/Validation/Test Split**: Stratified sampling for balanced datasets

## Model Performance

Current model achieves:
- **Mean Absolute Error**: ~$45 on test set
- **R² Score**: 0.65+ explaining price variance
- **Training Time**: <10 minutes on standard hardware
- **Inference Time**: <100ms for batch predictions

## Production Deployment

### Environment Management
```bash
# Clean up corrupted MLflow environments
conda info --envs | grep mlflow | cut -f1 -d" "
for e in $(conda info --envs | grep mlflow | cut -f1 -d" "); do conda uninstall --name $e --all -y; done
```

### Model Promotion
1. Train model using the pipeline
2. Evaluate performance metrics
3. Promote model to "prod" in MLflow Model Registry
4. Run test_regression_model step for final validation

## Development Guidelines

### Adding New Components
1. Create component directory in `components/`
2. Add `MLproject` file with parameters and entry points
3. Include `conda.yml` for dependencies
4. Update main pipeline configuration

### Parameter Tuning
All parameters are configurable via `config.yaml`. Use Hydra syntax for overrides:
```bash
mlflow run . -P hydra_options="modeling.random_forest.n_estimators=300 etl.max_price=500"
```

### Data Versioning
All intermediate data artifacts are tracked in Weights & Biases with:
- Dataset versions and checksums
- Feature engineering transformations
- Model training datasets
- Performance metrics history

## Monitoring and Maintenance

- **Data Drift Detection**: Automated KL-divergence tests between training and new data
- **Model Performance Tracking**: Continuous monitoring of prediction accuracy
- **Pipeline Health Checks**: Automated validation of each pipeline step
- **Alert System**: Notifications for pipeline failures or performance degradation

## Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/new-component`)
3. Make changes with appropriate tests
4. Update documentation
5. Submit pull request

## License

This project is licensed under the MIT License - see [LICENSE.txt](LICENSE.txt) for details.

## Project Structure

```
NYC-Airbnb-ML-Pipeline/
├── components/                 # Reusable pipeline components
│   ├── get_data/              # Data download component
│   ├── train_val_test_split/  # Data splitting component
│   └── test_regression_model/ # Model testing component
├── src/                       # Source code for custom components
│   ├── basic_cleaning/        # Data cleaning implementation
│   ├── data_check/           # Data validation implementation
│   └── train_random_forest/   # Model training implementation
├── images/                    # Documentation images
├── config.yaml               # Pipeline configuration
├── main.py                   # Pipeline orchestration
├── MLproject                 # MLflow project definition
├── environment.yml           # Conda environment specification
└── README.md                 # Project documentation
```

## Support

For questions or issues:
1. Check existing documentation and configuration
2. Review MLflow and Wandb logs for error details
3. Ensure all dependencies are correctly installed
4. Verify Weights & Biases authentication and project access