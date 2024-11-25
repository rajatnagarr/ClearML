Evaluation and Logging
======================

Evaluating and Logging Metrics
------------------------------

.. code-block:: python

   import matplotlib.pyplot as plt

   # Evaluate the model
   test_loss, test_accuracy = model.evaluate(test_input, test_labels)
   print(f"Test Loss: {test_loss}, Test Accuracy: {test_accuracy}")

   # Log metrics to ClearML
   task.get_logger().report_single_value("Test Loss", test_loss)
   task.get_logger().report_single_value("Test Accuracy", test_accuracy)

   # Log training history
   def plot_training(history):
       plt.figure()
       plt.plot(history.history['loss'], label='Train Loss')
       plt.plot(history.history['val_loss'], label='Validation Loss')
       plt.title('Loss Curve')
       plt.legend()
       plt.savefig('loss_curve.png')
       plt.show()

   plot_training(history)

   # Save the model
   model.save("trained_model.keras")
   task.upload_artifact("Trained Model", artifact_object="trained_model.keras")
