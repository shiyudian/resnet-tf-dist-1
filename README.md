
Reference to tensorflow official

# ResNet in TensorFlow

Deep residual networks, or ResNets for short, provided the breakthrough idea of
identity mappings in order to enable training of very deep convolutional neural
networks. This folder contains an implementation of ResNet for the cifar10
dataset written in TensorFlow.

## CIFAR-10

### Setup

You simply need to have the latest version of TensorFlow installed.
First make sure you've [added the models folder to your Python path](/official/#running-the-models); otherwise you may encounter an error like `ImportError: No module named official.resnet`.

``` bash
export PYTHONPATH="$PYTHONPATH:/home/ubuntu/models"
```

Then download and extract the CIFAR-10 data from Alex's website, specifying the location with the `--data_dir` flag. Run the following:

```bash
python cifar10_download_and_extract.py
```

Use `--data_dir` to specify the location of the CIFAR-10 data used in the previous step. There are more flag options as described in `cifar10_main.py`.




Note that there are a number of other options you can specify, including
`--model_dir` to choose where to store the model and `--resnet_size` to choose
the model size (options include ResNet-18 through ResNet-200). See
[`resnet.py`](resnet.py) for the full list of options.


### Pre-trained model
You can download pre-trained versions of ResNet-50. Reported accuracies are top-1 single-crop accuracy for the ImageNet validation set.
Models are reported as both checkpoints produced by Estimator during training, and as SavedModels which are more portable. Checkpoints are fragile,
and these are not guaranteed to work with future versions of the code. Both ResNet v1
and ResNet v2 have been trained in both fp16 and fp32 precision. (Here v1 refers to "v1.5". See the note above.) Furthermore, SavedModels
are generated to accept either tensor or JPG inputs, and with channels_first (NCHW) and channels_last (NHWC) convolutions. NCHW is generally
better for GPUs, while NHWC is generally better for CPUs. See the TensorFlow [performance guide](https://www.tensorflow.org/performance/performance_guide#data_formats)
for more details.


### Transfer Learning
You can use a pretrained model to initialize a training process. In addition you are able to freeze all but the final fully connected layers to fine tune your model. Transfer Learning is useful when training on your own small datasets. For a brief look at transfer learning in the context of convolutional neural networks, we recommend reading these [short notes](http://cs231n.github.io/transfer-learning/).


To fine tune a pretrained resnet you must make three changes to your training procedure:

1) Build the exact same model as previously except we change the number of labels in the final classification layer.

2) Restore all weights from the pre-trained resnet except for the final classification layer; this will get randomly initialized instead.

3) Freeze earlier layers of the network

We can perform these three operations by specifying two flags: ```--pretrained_model_checkpoint_path``` and ```--fine_tune```. The first flag is a string that points to the path of a pre-trained resnet model. If this flag is specified, it will load all but the final classification layer. A key thing to note: if both ```--pretrained_model_checkpoint_path``` and a non empty ```model_dir``` directory are passed, the tensorflow estimator will load only the ```model_dir```. For more on this please see [WarmStartSettings](https://www.tensorflow.org/versions/master/api_docs/python/tf/estimator/WarmStartSettings) and [Estimators](https://www.tensorflow.org/guide/estimators).

The second flag ```--fine_tune``` is a boolean that indicates whether earlier layers of the network should be frozen. You may set this flag to false if you wish to continue training a pre-trained model from a checkpoint. If you set this flag to true, you can train a new classification layer from scratch.
