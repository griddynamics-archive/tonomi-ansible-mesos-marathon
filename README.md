# Mesos/Marathon cluster deployment support
Released as a part of In-Stream Processing Blueprint Starter Kit for Tonomi


### Installs and configures Mesos/Marathon cluster
  - manually
  - using Tonomi platform

### How to:
#### Manual installation:
- clone the repository
  
  ```
  git clone https://github.com/griddynamics/tonomi-ansible-mesos-marathon.git
  cd tonomi-ansible-mesos-marathon
  ```
  
- prepare your infrastructure:
  * provision couple of Ubuntu-based VMs on Amazon or other cloud provider
  * OR collect list of existing Ubuntu-based nodes' IP addresses
  * prepare/generate ssh key suitable for accessing all your VMs
  * if there's a new key, make sure it is distributed across VMs first
- prepare the hosts file(s) for Ansible, substituting placeholders with hostnames/IP addresses, username, and path to SSH key respectively:
  * sample command-line assumes separate folder *inventory* with lists of masters and slaves for Mesos/Marathon cluster
    ```
    mkdir inventory && cd inventory
    ```
  * create files with lists of future mesos-masters and mesos-slaves
    ```
    $cat masters
    
    [mesos_masters]
    << list of ip addresses goes here >>

    [mesos_masters:vars]
    ansible_user= << username for accessing your VMs >>
    ansible_ssh_private_key_file= << path to the SSH key >>
    ```

    ```
    $cat slaves
    
    [mesos_workers:children]
    mesos_general_instances
    mesos_cpu_instances
    mesos_memory_instances
    mesos_disk_instances
    
    # this section describes different 'flavors' for mesos-slaves. 
    # distribute the list of your VMs across these sections 
    # (you can use only one if for instance only 'general-purpose' are required)
    
    [mesos_general_instances]

    [mesos_cpu_instances]

    [mesos_memory_instances]

    [mesos_disk_instances]

    # end of mesos-slaves list(s)
    
    [mesos_workers:vars]
    ansible_user= << username for accessing your VMs >>
    ansible_ssh_private_key_file= << path to the SSH key >>
    
    # the section below is common - those are just labels assigned to different flavors of mesos-slaves
    [mesos_general_instances:vars]
    mesos_node_type=general
    [mesos_cpu_instances:vars]
    mesos_node_type=cpu-intensive
    [mesos_memory_instances:vars]
    mesos_node_type=memory-intensive
    [mesos_disk_instances:vars]
    mesos_node_type=disk-intensive
    ```
    
    ```
    # don't forget to get back to 'root' folder
    cd ..
    ```
- create parameters (_params.yaml_) file for Ansible with "configuration" values - versions of packages and 2nd list of masters (this list is required in order to correctly assemble Zookeeper ensemble):
  ```
  cat params.yaml
  
  mesos_version: "0.28.2"
  mesos_package_version: "2.0.27"
  marathon_version: "1.1.2"
  # please note this is YAML list, so every item should start with dash "-"
  mesos_master_hosts:
  - <<ip>>

  ```

- run Ansible: 
    ```
    ansible-playbook -i ./inventory ./playbook.yml --extra-vars "@./params.yaml
    ```



#### Using Tonomi platform:
Just click the button below in order to import the manifest provided.

[![Install](https://raw.github.com/qubell-bazaar/component-skeleton/master/img/install.png)](https://express.tonomi.com/applications/upload?metadataUrl=https://raw.githubusercontent.com/griddynamics/tonomi-ansible-mesos-marathon/master/manifest.yml)
