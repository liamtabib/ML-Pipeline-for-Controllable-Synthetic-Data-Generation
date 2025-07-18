# ML Pipeline for Controllable Synthetic Data Generation

**[Official Published Paper](https://www.diva-portal.org/smash/record.jsf?pid=diva2:1887438)**

This project is a machine learning pipeline that generates realistic, controllable synthetic faces—providing a low-cost stream of diverse, high-quality training data for computer vision models. Traditional facial datasets are expensive to collect and lack the specific variations needed for robust model training, particularly for rare combinations of identity, lighting, and pose attributes.

The pipeline controls facial attribute generation at the feature level by separating identity features from environmental factors like lighting and pose, allowing independent manipulation of these characteristics during synthetic face generation.

Built on StyleGAN3 with custom disentanglement techniques, the system produces realistic images where you can systematically vary specific attributes while holding others constant—ideal for creating controlled datasets and testing model robustness. The architecture combines pre-trained StyleGAN3 encoders with trainable NICE transformation networks.

This enables engineers to expand their training datasets with exactly the attribute combinations they need, reducing data collection costs while improving model performance. This is particularly valuable for bias testing, edge case generation, and creating balanced datasets across demographics.

## Quick Setup

Install dependencies and datasets:

```bash
# Install the package in development mode
pip install -e .

# Automatically setup external dependencies (StyleGAN3, InsightFace, etc.)
chmod +x scripts/setup_deps.sh && ./scripts/setup_deps.sh
```

You'll also need to download the [CelebAMask-HQ dataset](https://github.com/switchablenorms/CelebAMask-HQ) to `datasets/celebahq/` and place pre-trained models in `projects/disentanglement/pretrained_models/`.

Start training:
```bash
python projects/disentanglement/train.py
```

Training uses `config.yaml` settings to learn disentangled representations.

## How It Works

The core idea is pretty simple: we use a NICE (Non-linear Independent Components Estimation) network to create bijective transformations that separate identity information from other facial attributes in StyleGAN3's latent space. 

DisGAN combines a pre-trained StyleGAN3 encoder with a trainable NICE transformation. Training uses multiple loss functions:

- **Switch loss** maintains identity consistency across transformations
- **Contrastive loss** pushes different identities apart in latent space  
- **Landmark loss** preserves facial structure
- **Discriminator loss** creates adversarial dynamics to separate identity from attributes

Evaluation uses face recognition distance, DCI metrics, and PCA analysis to measure disentanglement quality.

## Configuration

Everything's controlled through `config.yaml`: batch sizes, learning rates, loss weights, dataset paths, and loss function toggles. No code changes required for experimentation.

## Project Structure

```
projects/disentanglement/
├── train.py                    # Main training script
├── src/
│   ├── data/                  # Dataset handling  
│   ├── metrics/               # Evaluation metrics
│   ├── visualisations/        # Analysis tools
│   ├── models.py              # StyleGAN3, NICE, DisGAN definitions
│   └── losses.py              # Loss function implementations
```

## Console Commands

Available commands:
```bash
train-disentanglement          # Main training
train-discriminators           # Train identity discriminators
```

## Common Issues

- **Import errors**: Run `pip install -e .` first
- **Missing dependencies**: Run `./scripts/setup_deps.sh`  
- **CUDA problems**: Check device settings in `config.yaml`
- **Dataset not found**: Verify paths in `config.yaml`

