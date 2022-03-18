[How to inspect a pre-trained TensorFlow model](https://daj.medium.com/how-to-inspect-a-pre-trained-tensorflow-model-5fd2ee79ced0)

[Use tensorboard in jupyter notebook docker](https://www.tensorflow.org/tensorboard/tensorboard_in_notebooks)
```
docker run -it -p 8888:8888 -p 6006:6006 tensorflow/tensorflow:nightly-py3-jupyter

# Load the TensorBoard notebook extension
%load_ext tensorboard

%tensorboard --logdir logs

from tensorboard import notebook
notebook.list() # View open TensorBoard instances

# Control TensorBoard display. If no port is provided,
# the most recently launched TensorBoard is used
notebook.display(port=6006, height=1000)
```
