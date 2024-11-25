Model Training
==============

Creating and Training the Model
-------------------------------

.. code-block:: python

   import tensorflow as tf
   from tensorflow.keras import layers
   import numpy as np

   # Assuming 'combined_data' is your preprocessed dataset
   data = combined_data.to_numpy()

   # Split into training, validation, and test sets
   train_size = int(0.7 * len(data))
   val_size = int(0.15 * len(data))
   test_size = len(data) - train_size - val_size

   train_data = data[:train_size]
   val_data = data[train_size:train_size+val_size]
   test_data = data[train_size+val_size:]

   train_input, train_labels = train_data[:, :-1], train_data[:, -1]
   val_input, val_labels = val_data[:, :-1], val_data[:, -1]
   test_input, test_labels = test_data[:, :-1], test_data[:, -1]

   # Create the model
   model = tf.keras.Sequential([
       layers.Dense(32, activation='relu', input_shape=(train_input.shape[1],)),
       layers.Dense(16, activation='relu'),
       layers.Dense(8, activation='relu'),
       layers.Dense(3, activation='softmax')  # Adjust '3' to the number of classes in your dataset
   ])

   model.compile(optimizer='adam',
                 loss=tf.keras.losses.SparseCategoricalCrossentropy(),
                 metrics=['accuracy'])

   # Train the model
   history = model.fit(
       train_input, train_labels,
       validation_data=(val_input, val_labels),
       batch_size=64,
       epochs=15,
       callbacks=[
           tf.keras.callbacks.EarlyStopping(monitor='val_loss', patience=5),
           tf.keras.callbacks.ReduceLROnPlateau(monitor='val_loss', factor=0.1, patience=3)
       ]
   )

   print("Training complete!")
