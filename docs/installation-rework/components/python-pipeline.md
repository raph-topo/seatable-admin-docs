## Activate the python pipeline on a one node seatable server
This guide shows how to activate the python pipeline on a one node seatable server. If you are using a separate node to run the python-pipeline you have to account for the different hostnames and ports.

#### 1. Change the .env file

Add _seatable-python-pipeline.yml_ to the COMPOSE_FILE variable.

```bash
nano /opt/seatable-compose/.env
```

Your COMPOSE_FILE variable should look something like this:
```bash
COMPOSE_FILE='seatable-docker-proxy.yml,seatable-server.yml,seatable-python-pipeline.yml'
```
#### 2. Generate inital secret

Generate inital secrets and write them into your .env file.

```bash
pw=$(pwgen -s 40 1) && echo "Generated shared secret: ${pw}"
```
Execute the following command to add the shared secret to the `.env` file:

```bash
echo "PYTHON_SCHEDULER_AUTH_TOKEN=${pw}" >> /opt/seatable-compose/.env
```

Now execute this command to add the required configuration to `dtable_web_settings.py`:

```bash
echo "SEATABLE_FAAS_URL = 'http://seatable-python-scheduler'" >> /opt/seatable-server/seatable/conf/dtable_web_settings.py
echo "SEATABLE_FAAS_AUTH_TOKEN = '${pw}'" >> /opt/seatable-server/seatable/conf/dtable_web_settings.py
```

#### 3. Start the Python Pipeline

Be aware that the SEATABLE_SCHEDULER_HOSTNAME variable has to be set in your .env before the first start of the container to generate all the configuration files correctly.  

```bash
cd /opt/seatable-compose && \
docker compose down && \
docker compose up -d && \
docker logs -f seatable-python-scheduler
```
If you see _"Start Monitor"_ the scheduler was started successfully.  
You can return to a prompt with `CTRL + c`

#### 4. Check if the python pipeline is running

create a new base  
add a python script -> for example `print("Hello World!")`  
check the output  -> expected output is: `Hello World!`
