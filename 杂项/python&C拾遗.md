```python
folder = 'tfrecords-jpeg-224x224'
train_files = tf.io.gfile.glob(f"/kaggle/input/tpu-getting-started/{folder}/train/*.tfrec")
val_files = tf.io.gfile.glob(f"/kaggle/input/tpu-getting-started/{folder}/val/*.tfrec")
test_files = tf.io.gfile.glob(f"/kaggle/input/tpu-getting-started/{folder}/test/*.tfrec")
```
### parse_args(argc, argv);
![[Pasted image 20250822213412.png]]
