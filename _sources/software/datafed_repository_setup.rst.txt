DataFed Repository Setup Guide
==============================


*Last updated by Theo Beers, 2024-08-26, 20:00 EDT*

1. Login to the Server
----------------------

- Start the server.
- SSH into the server:

  ```bash
  ssh root@<server-ip>
  ```

2. Create a New User
--------------------

- Add a new user:

  ```bash
  adduser <username>
  ```

  - Set the password when prompted.

3. Add User to the `sudo` Group
-------------------------------

- Grant the user sudo privileges:

  ```bash
  usermod -aG sudo <username>
  ```

4. Switch to the New User
-------------------------

- Switch to the newly created user:

  ```bash
  su - <username>
  ```

5. Update the System
--------------------

- Update package information and upgrade the system:

  ```bash
  sudo apt update
  sudo apt install python3-venv  # Required for later steps
  sudo apt upgrade
  ```

6. Reboot the Server
--------------------

- Reboot the server and re-enter:

  ```bash
  sudo reboot
  ```

  After reboot, SSH in again and switch to the user:

  ```bash
  ssh root@<server-ip>
  su - <username>
  ```

7. Install Globus Connect Server
--------------------------------

- Download and install the Globus Connect Server:

  ```bash
  curl -LOs https://downloads.globus.org/globus-connect-server/stable/installers/repo/deb/globus-repo_latest_all.deb
  sudo dpkg -i globus-repo_latest_all.deb
  sudo apt-key add /usr/share/globus-repo/RPM-GPG-KEY-Globus
  sudo apt update
  sudo apt install globus-connect-server54
  ```

8. Install Docker
-----------------

- Install Docker prerequisites:

  ```bash
  sudo apt install ca-certificates curl
  sudo install -m 0755 -d /etc/apt/keyrings
  sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
  sudo chmod a+r /etc/apt/keyrings/docker.asc
  ```

- Set up the Docker repository:

  ```bash
  echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  ```
  
- Install Docker:

  ```bash
  sudo apt update
  sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
  ```

- Add the user to the `docker` group:

  ```bash
  sudo usermod -aG docker <username>
  ```

9. Reboot and Test Docker
-------------------------

- Reboot the server and re-enter:

  ```bash
  sudo reboot
  ssh root@<server-ip>
  su - <username>
  sudo apt update
  sudo apt upgrade
  ```

- Test Docker:

  ```bash
  docker run hello-world
  ```

10. Clone the DataFed Repository
--------------------------------

- Clone the DataFed repository:

  ```bash
  git clone https://github.com/ORNL/DataFed.git
  cd DataFed
  git checkout release_June_2024
  git submodule update --init --recursive
  ```

11. Fix Dockerfile Permissions Issue
------------------------------------

- Fix permissions in the Dockerfile:

  ```bash
  sed -i '/WORKDIR/a RUN chmod o+w ${DATAFED_INSTALL_PATH}/authz' repository/docker/Dockerfile.gcs
  ```

12. Navigate to the Working Directory
-------------------------------------

- Navigate to the `compose/repo` directory:

  ```bash
  cd compose/repo
  ```

  All subsequent steps assume you remain in ``DataFed/compose/repo``.

13. Set Up Python Virtual Environment
-------------------------------------

- Set up a Python virtual environment:

  ```bash
  python3 -m venv .venv
  source .venv/bin/activate
  pip install globus-sdk
  ```

14. Generate and Edit the `.env` File
-------------------------------------

- Generate the `.env` file:

  ```bash
  ./generate_env.sh
  ```

- Edit the `.env` file with the appropriate values:

  ```bash
  DATAFED_GLOBUS_SUBSCRIPTION=<id>
  DATAFED_DOMAIN=datafed.ornl.gov
  DATAFED_GCS_IP=<server-ip>
  DATAFED_REPO_DOMAIN=<server-ip>
  ```

15. Generate Globus Files
-------------------------

- Generate Globus configuration files:

  ```bash
  ./generate_globus_files.sh
  ```

  Follow the provided link, retrieve the authorization code, and paste it when prompted.

16. Build Docker Images
-----------------------

- Build the repository Docker images:

  ```bash
  ./build_repo_images_for_compose.sh
  ```

  This process may take at least 15 minutes.

17. Ensure Apache is Not Running
--------------------------------

- Check if Apache is running:

  ```bash
  sudo apt install net-tools
  sudo netstat -tlupn | grep ':80'
  ```

- If Apache is running, stop the service:

  ```bash
  sudo systemctl stop apache2
  ```

  Double-check the status to ensure it is stopped:

  ```bash
  sudo netstat -tlupn | grep ':80'
  sudo systemctl status apache2
  ```

18. Start Docker Compose
------------------------

- Unset environment variables (optional, but may be necessary):

  ```bash
  source ./unset_env.sh
  ```

- Start the Docker Compose setup:

  ```bash
  docker compose -f ./compose.yml up
  ```

  With the permission issue fixed, it should now work as expected.

