Setup
=====

Before Getting Started
----------------------

1. **Check the Deployment**: Ensure that the ClearML server and ClearML agent are deployed in virtual machines (VMs) within the OpenStack project.

   *If any of the components are missing, please contact the administrator.*

   - **ClearML Server**: The backend service infrastructure for ClearML. It allows multiple users to collaborate and manage their experiments by working seamlessly with the ClearML Python package and ClearML Agent.

     **Components**:

     - **Web Server**: Includes the ClearML Web UI, which is the user interface for tracking, comparing, and managing experiments.
     - **API Server**: A RESTful API for:
       - Documenting and logging experiments, including information, statistics, and results.
       - Querying experiment history, logs, and results.
     - **File Server**: Stores media and models, making them easily accessible via the ClearML Web UI.

2. **Communicate with the ClearML Server** [2]_:

   - **Create a VM in the OpenStack Project**:

     **Requirements**:

     - Ubuntu 20.04
     - Flavor: 8 CPU, 8 GB RAM, 128 GB Disk

   - **Create a Virtual Environment**:

     .. code-block:: bash

        sudo apt-get update
        sudo apt-get install python3-venv
        python3 -m venv myenv

   - **Activate the Virtual Environment**:

     .. code-block:: bash

        source myenv/bin/activate

   - **Install the ClearML Python Package**:

     .. code-block:: bash

        pip install clearml

   - **Connect the ClearML SDK to the Server**:

     - Run the ClearML setup wizard:

       .. code-block:: bash

          clearml-init

     - The setup wizard will prompt for ClearML credentials:

       *"Please create new ClearML credentials through the settings page in your `clearml-server` web app (e.g., http://localhost:8080/settings/workspace-configuration), or create a free account at https://app.clear.ml/settings/workspace-configuration. In the settings page, press 'Create new credentials', then press 'Copy to clipboard'. Paste the copied configuration here:"*

       - **Note**: To get credentials, please contact the administrator.
       - At the command prompt, paste the copied ClearML credentials. The setup wizard will verify the credentials.

     - **Sample Output**:

       .. code-block:: text

          Detected credentials key="********************" secret="*******"
          CLEARML Hosts configuration:
              Web App: https://app.<your-domain>
              API: https://api.<your-domain>
              File Store: https://files.<your-domain>
          Verifying credentials ...
          Credentials verified!
          New configuration stored in /home/<username>/clearml.conf
          CLEARML setup completed successfully.

     - You are now ready to use ClearML in your code!
