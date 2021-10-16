## Intent
This document is inteded for those who are trying to deploy an asp.net core application on Raspberry pi or Azure percept. It will also guide on how to deploy the application as an iot edge module. This document assumes that you are familier with setting up Azure Percept or Raspberry device as an iot edge device and have access to SSH into it. This document also assumes that you are somewhat familier with developing and deploying iot edge modules, docker, docker hub or Azure Container Registry.

### Prerequisites
1. Raspberry pi or Azure percept
1. Azure Subscription with azure services setup - IoT Hub, Azure Container Registry.
1. Setup both as an edge device; for Percept you can use the Azure Percept's documentation to set it up which eventually will have percept as an edge device.
1. VS Code
1. VS Code Extensions - Remote SSH, Azure IoT Tools.

## Setup
1. Start by logging in to pi or azure percept using VS Code Remote SSH.
1. Install Azure IoT Tools extension for VS Code only when connected with device using remote ssh. This will install the extension on the device instead of your local machine.
1. **Edge Solution**:</br>
   Press the F1 or Ctrl+Shift+P button to bring up the Command Pallet and search for `Azure IoT Edge: New IoT Edge Solution` and follow the steps to create a sample Solution with a default module. When prompted for hosting, provide the Azure Container Registry host i.e. `acs-registryname.azurecr.io` or docker hub registry name instead of `localhost:5000`. This will have following folder structure created.
   ![Edge Solution](./images/EdgeSolution.png)</br>
   - The **modules** folder contains all the modules that you can develop in a single solution. Each module contains dockerfiles targetting different archs to generate images. We will be using arm32v7 for percept or pi. Each module has the boiler plate project tempalte already created.
   - The `.env` file contains the environment variables for your module deployment. It also has keys for your container registry (name, username, password). This file is configuration can be shared across modules in your solution.
     ![.env file](./images/EnvironmentFile.png)</br>
   - The `deployment.template.json` contains the deployment settings for all your modules. Using this file you can build entire solution, generate module images, push images to your registry and create deployment manifest for solution deployment.
1. Once the solution is created, open command pallete and use `Azure Iot Edge: Set Default Target Platform for Edge Solution` set the correct architecture to target from the availabe options. In our case it is `arm32v7`.
1. Add a new folder `webmodule` inside modules folder and Create a new asp.net core mvc solution inside it.
1. Since this is a `webmodule`, we need to add create options in the `deployment.template.json`. The create options enables the us to access the webmodule container from the outside using `localhost:8080` or using `<device-ip-address>:8080` Add below
   ```json
   {
        "HostConfig": {
            "PortBindings": {
                "80/tcp": [ 
                    { 
                        "HostPort": "8080"
                    } 
                ] 
            } 
        }
   }
   ```
1. 
