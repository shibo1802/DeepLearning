[Install Tensorflow - Build from source](https://www.tensorflow.org/install/source)

[Install Tensorflow - GPU support](https://www.tensorflow.org/install/gpu)

[Install Tensorflow - GPU Docker](https://www.tensorflow.org/install/docker)
```
docker pull boyue1802/deep_learning:tensorflow_1.15.5-gpu-py3-jupyter
```

[Use a GPU](https://www.tensorflow.org/guide/gpu)
```
import tensorflow as tf
print("Num GPUs Available: ", len(tf.config.experimental.list_physical_devices('GPU')))
```