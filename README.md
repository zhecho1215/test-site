<div style="text-align: justify;">

<h1 align="center">
Trajpred
</h1>
<p align="center">
<a href="https://github.com/vita-epfl/TrajPred/actions/workflows/main.yml">
  <img src="https://github.com/vita-epfl/TrajPred/actions/workflows/main.yml/badge.svg" alt="Build and Test" />
</a>
<br>
A Pytorch-based trajectory prediction framework for multiple datasets
</p>

## Installation
  
If you want to use this framework and edit the code, Please clone the repository, and use the command below:
```bash
pip install -e .
``` 
  
Otherwise:
```bash
pip install git+https://github.com/vita-epfl/TrajPred@master
```
  
## Usage

### Training

```bash
usage: trajpred-train [-h] entrypoint

Train executable of TrajPred

positional arguments:
  entrypoint  Entrypoint to be run

optional arguments:
  -h, --help  show this help message and exit
```

Note: Entrypoint is a class that prepares everything is needed for training or evaluating a model on some specific datasets. It connects the model, datasets, configs, optimizer, output saver, and criterion.

### Example Entrypoint

```bash
usage: trajpred-train trajpred.baselines.cmu_datf.entrypoints.argo_entrypoint [-h] [-c CONFIG] [-l {best,last}] [-b BATCH_SIZE]
                                                   [-cd CHECKPOINT_DIRECTORY] [-d DEVICE] [-e MAX_EPOCHS]
                                                   [-w LOAD_NUM_WORKERS] [-lr LEARNING_RATE]
                                                   [-ls ITERATION_LOG_STEP]

optional arguments:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        JSON config file
  -l {best,last}, --load-type {best,last}
                        Checkpoint load type
  -b BATCH_SIZE, --batch-size BATCH_SIZE
                        Training Batch size
  -eb EVAL_BATCH_SIZE, --eval-batch-size EVAL_BATCH_SIZE
                        Validation / Evaluation Batch size
  -cd CHECKPOINT_DIRECTORY, --checkpoint-dir CHECKPOINT_DIRECTORY
                        Checkpoint directory
  -d DEVICE, --device DEVICE
                        Device to use
  -e MAX_EPOCHS, --max-epochs MAX_EPOCHS
                        Max epochs
  -w LOAD_NUM_WORKERS, --load-num-workers LOAD_NUM_WORKERS
                        Number of workers for loading data (For multi-CPU Processing)
  -lr LEARNING_RATE, --learning-rate LEARNING_RATE
                        Learning rate
  -ls ITERATION_LOG_STEP, --iteration-log-step ITERATION_LOG_STEP
                        Step for logging iteration metrics
```

For the above example, passing a config file is necessary. This config file is as shown below:

```JSON
{
  "checkpoint_directory": "results/lanegcn",
  "batch_size": 128,
  "other_configs": {
    "opt": "adam",
    "lrs": [1e-3, 1e-4],
    "lr_step_iters": [51488],

    "train_split": "<path-to-argoverse-dataset>/train/data",
    "val_split": "<path-to-argoverse-dataset>/val/data",
    "test_split": "<path-to-argoverse-dataset>/test_obs/data",

    "preprocess": true,
    "preprocess_train": "<path-to-preprocess-root>/dataset/preprocess/train_crs_dist6_angle90.p",
    "preprocess_val": "<path-to-preprocess-root>/dataset/preprocess/val_crs_dist6_angle90.p",
    "preprocess_test": "<path-to-preprocess-root>/dataset/preprocess/test_test.p",

    "rot_aug": false,
    "pred_range": [-100.0, 100.0, -100.0, 100.0],
    "num_scales": 6,
    "n_actor": 128,
    "n_map": 128,
    "actor2map_dist": 7.0,
    "map2actor_dist": 6.0,
    "actor2actor_dist": 100.0,
    "num_preds": 30,
    "num_mods": 6,
    "cls_coef": 1.0,
    "reg_coef": 1.0,
    "mgn": 0.2,
    "cls_th": 2.0,
    "cls_ignore": 0.2
  }
}
```

So, you can run the following command to train the lanegcn model:

```bash
trajpred-train trajpred.entrypoints.lanegcn -c lanegcn-config.json
```

Also, you can override the main configs by passing them as command-line arguments. For example, to override the batch size, you can run the following command:

```bash
trajpred-train trajpred.entrypoints.lanegcn -c lanegcn-config.json -b 256
```

### Continue Training

You can continue training from a checkpoint by passing `--load-type` or `-l` as a command-line argument. For example, to continue training from the last epoch checkpoint, you can run the following command:

```bash
trajpred-train trajpred.entrypoints.lanegcn -c lanegcn-config.json -l last
```

### Evaluate


```bash
usage: trajpred-eval trajpred.entrypoints.lanegcn [-h] [-c CONFIG] [-l {best,last}] [-b BATCH_SIZE]
                                                  [-cd CHECKPOINT_DIRECTORY] [-d DEVICE] [-e MAX_EPOCHS]
                                                  [-w LOAD_NUM_WORKERS] [-lr LEARNING_RATE]
                                                  [-ls ITERATION_LOG_STEP]

optional arguments:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        JSON config file
  -l {best,last}, --load-type {best,last}
                        Checkpoint load type
  -b BATCH_SIZE, --batch-size BATCH_SIZE
                        Training Batch size
  -eb EVAL_BATCH_SIZE, --eval-batch-size EVAL_BATCH_SIZE
                        Validation / Evaluation Batch size
  -cd CHECKPOINT_DIRECTORY, --checkpoint-dir CHECKPOINT_DIRECTORY
                        Checkpoint directory
  -d DEVICE, --device DEVICE
                        Device to use
  -e MAX_EPOCHS, --max-epochs MAX_EPOCHS
                        Max epochs
  -w LOAD_NUM_WORKERS, --load-num-workers LOAD_NUM_WORKERS
                        Number of workers for loading data (For multi-CPU Processing)
  -lr LEARNING_RATE, --learning-rate LEARNING_RATE
                        Learning rate
  -ls ITERATION_LOG_STEP, --iteration-log-step ITERATION_LOG_STEP
                        Step for logging iteration metrics
```


### Save outputs

```bash
usage: trajpred-save-outputs trajpred.entrypoints.lanegcn [-h] [-c CONFIG] [-l {best,last}] [-b BATCH_SIZE]
                                                          [-cd CHECKPOINT_DIRECTORY] [-d DEVICE] [-e MAX_EPOCHS]
                                                          [-w LOAD_NUM_WORKERS] [-lr LEARNING_RATE]
                                                          [-ls ITERATION_LOG_STEP]

optional arguments:
  -h, --help            show this help message and exit
  -c CONFIG, --config CONFIG
                        JSON config file
  -l {best,last}, --load-type {best,last}
                        Checkpoint load type
  -b BATCH_SIZE, --batch-size BATCH_SIZE
                        Training Batch size
  -eb EVAL_BATCH_SIZE, --eval-batch-size EVAL_BATCH_SIZE
                        Validation / Evaluation Batch size
  -cd CHECKPOINT_DIRECTORY, --checkpoint-dir CHECKPOINT_DIRECTORY
                        Checkpoint directory
  -d DEVICE, --device DEVICE
                        Device to use
  -e MAX_EPOCHS, --max-epochs MAX_EPOCHS
                        Max epochs
  -w LOAD_NUM_WORKERS, --load-num-workers LOAD_NUM_WORKERS
                        Number of workers for loading data (For multi-CPU Processing)
  -lr LEARNING_RATE, --learning-rate LEARNING_RATE
                        Learning rate
  -ls ITERATION_LOG_STEP, --iteration-log-step ITERATION_LOG_STEP
                        Step for logging iteration metrics
```


## Use custom models and datasets

### 1. Start your project

```bash
mkdir project && cd project
```

### 2. Implement your model

Models must be inherited from the [`trajpred.model.TrajPredModel`](./docs/model.md#class-trajpredmodel) abstract class. You should pass the scene embeddings, data selectors, and the embedding size to the TrajPredModel's `__init__` method.

### 3. Prepare your datasets

Datasets must be inherited from the `torch.utils.data.Dataset` abstract class. The `__get_item__` method in datasets which have ground truth, must be tuple of (name: str, input: TPTensor, target: TPTensor), and in datasets which have not ground truth (just can be used in `trajpred-save-outputs`) must be a tuple of (name: str, input: [TPTensor](./docs/tptensor.md#type-TPTensor)).

### 4. Implement you output saver (if save-outputs will be used)

Output savers must be inherited from the trajpred.output_saver.OutputSaver abstract class.

### 5. Implement your entrypoint

Entrypoint must be inherited from the [`trajpred.entrypoint.BaseEntrypoint`](./docs/entrypoint.md#class-baseentrypoint) abstract class.

**Important Notes:**

- Each entrypoint MUST be imlemented in a separate file
- The name of inherited class MUST be `Entrypoint`
- The constructor of inherited class MUST NOT take any manadatory arguments
- The file name of an entrypoint represents the name of the entrypoint in `trajpred-train`, `trajpred-eval`, and `trajpred-save-outputs` commands. For example, if the entrypoint file is in `project/entrypoints/my_entrypoint.py`, then the entrypoint name in the commands will be `project.entrypoints.my_entrypoint`.




</div>
