# Docker mounting

To configure Docker to use a custom daemon.json file for mounting volumes and other settings, you can follow these steps:

1. **Create or Locate Your Custom daemon.json File**:
   * You can create a custom `daemon.json` file or locate an existing one. This file will contain your Docker daemon configuration options.
2.  **Edit or Create daemon.json**:

    * Open the `daemon.json` file using a text editor. If the file doesn't exist, you can create it. The typical location for this file is `/etc/docker/daemon.json` on Linux systems.
    * Here is an example of what the `daemon.json` file might look like:



```bash
{
  "data-root": "/docker/data", //you can modify this data-root directory
  "storage-driver": "overlay2",
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}

```

Customize the settings in the `daemon.json` file as needed. In the example above:

* `"data-root"` specifies the directory where Docker stores its data, including images and containers.
* `"storage-driver"` sets the storage driver used by Docker.
* `"log-driver"` specifies the logging driver.
* `"log-opts"` allows you to set specific options for the logging driver.

3. **Restart Docker**:

```bash
sudo systemctl restart docker
```

4. **Verify the Configuration**:

```bash
docker info
```
