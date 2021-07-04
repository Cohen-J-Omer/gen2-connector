**gen2-connector provides Ray plugin to manage clusters over ibm gen2 infrastructure**

1. Install [Ray](https://github.com/ray-project/ray) release 1.4.1 using `pip install ray==1.4.1`

2. Generate ssh public/private keys, e.g. `using ssh-keygen`

    * Add ssh public key to ibm vpc, either using IBM Cloud Portal or cli
    
    ```
    ibmcloud is key-create my-key @/tmp/my_id_rsa.pub
    ```

    * Configure same public key to access github and validate you able to clone this repository

    * `export SSH_KEY=/path/to/ssh/private/key` #e.g. ~/.ssh/id_rsa

3. Install gen2-connector on your machine

```
export GIT_SSH_COMMAND="ssh -i ${SSH_KEY}"
git clone git@github.com:project-codeflare/gen2-connector.git
cd gen2-connector
pip install .
```

4. Configure ibm vpc
    * [Generate API KEY](https://www.ibm.com/docs/en/spectrumvirtualizecl/8.1.3?topic=installing-creating-api-key)

    * [Create/Update security group](https://cloud.ibm.com/docs/vpc?topic=vpc-configuring-the-security-group) to have SSH, Redis and Ray Dashboard ports open: 22, 8265 and 6379

5. Create cluster config file

    * Use interactive `vpc_config` tool to generate cluster.yaml configuration file
    ```
    vpc_config --iam_api_key ${IAM_API_KEY} --format ray
    ```
    
    * Select security group from previous step when prompted

    * Provide full path to Private SSH_KEY when prompted 

    * The output of the `vpc_config` is a cluster config yaml file, e.g
    ```
    =================================================
    Cluster config file: /tmp/tmpkf0dztfk.yaml
    =================================================
    ```

    * Alternatively, update cluster config manually based on [defaults.yaml](templates/defaults.yaml)
    
6. Use generated file to bring ray cluster up, e.g

```ray up /tmp/tmpkf0dztfk.yaml```

* After finished, use browser to open ray dashboard on PUBLIC_HEAD_IP:8265

* Submit example task `ray submit /tmp/tmpkf0dztfk.yaml templates/example.py`
