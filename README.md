
code modified based on tensorflow official resnet model

# ResNet in TensorFlow

Deep residual networks, or ResNets for short, provided the breakthrough idea of
identity mappings in order to enable training of very deep convolutional neural
networks. This folder contains a distributed implementation of ResNet for the cifar10
dataset written in TensorFlow. 

## CIFAR-10

### Setup

You simply need to have the latest version of TensorFlow installed.
First make sure you've [added the models folder to your Python path](/official/#running-the-models); otherwise you may encounter an error like `ImportError: No module named official.resnet`.

``` bash
export PYTHONPATH="$PYTHONPATH:/home/ubuntu/models"
```
### Download the dataset
Then download and extract the CIFAR-10 data from Alex's website, specifying the location with the `--data_dir` flag. Run the following:

```bash
python cifar10_download_and_extract.py
```

Use `--data_dir` to specify the location of the CIFAR-10 data used in the previous step. There are more flag options as described in `cifar10_main.py`.

### Start training distributed ResNet model
 This distributed training contains 1 parameter_servers (ps) and 3 workers as an example. Number of parameter_server and wokers are stored in `parameter_servers` and `workers` lists respectively. Users have to open cooresponding number of terminal windows in order to start each training task.
 
1. ssh to the remote machines
2. activivate tensorflow
3. killall python - *optional
4. in each window, type in the following cmd individually to start training task
 ```bash
killall python
source activate tensorflow
python cifar10_main.py --job_name="ps" --task_index=0
python cifar10_main.py --job_name="worker" --task_index=0
python cifar10_main.py --job_name="worker" --task_index=1 
python cifar10_main.py --job_name="worker" --task_index=2 
 ```
5. after training tasks are done, kill the ps port by its id as follows:
```bash
lsof -wni tcp:2222
# will return process ids
kill id
```
