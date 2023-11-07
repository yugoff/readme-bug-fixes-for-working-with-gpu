# readme-bug-fixes-for-working-with-gpu

## Возможные ошибки и их исправления.

### 1. Команда для запуска контейнера в Docker с доступом к графическому процессору (GPU) с использованием NVIDIA: 
	
	```bash
	sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
	```
	
	+-----------------------------------------------------------------------------+
	| NVIDIA-SMI 535.86.10    Driver Version: 535.86.10    CUDA Version: 12.2     |
	|-------------------------------+----------------------+----------------------+
	| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
	| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
	|                               |                      |               MIG M. |
	|===============================+======================+======================|
	|   0  Tesla T4            On   | 00000000:00:1E.0 Off |                    0 |
	| N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
	|                               |                      |                  N/A |
	+-------------------------------+----------------------+----------------------+

	+-----------------------------------------------------------------------------+
	| Processes:                                                                  |
	|  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
	|        ID   ID                                                   Usage      |
	|=============================================================================|
	|  No running processes found                                                 |
	+-----------------------------------------------------------------------------+
	
	Если вывод не в виде таблицы и возникают ошибки, рекомендуется выполнить следующие действия:
	
	1.1. Удалите пакет NVIDIA Container Toolkit:
	
	```bash
	   sudo apt remove nvidia-container-toolkit
	```
	
	1.2. Удалите пакеты Docker и containerd:
	
	```bash
	   sudo apt remove docker-ce docker-ce-cli containerd.io
	```
	
	1.3. Базовая процедура установки Docker:
	
	```bash
	   sudo apt update
	   sudo apt install apt-transport-https ca-certificates curl software-properties-common
	   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
	   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
	   sudo apt update
	   sudo apt install docker-ce docker-ce-cli containerd.io
	```
	
	1.4. Базовая процедура установки NVIDIA Container Toolkit:
	
	```bash
	   distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
	   curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
	   curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
	   sudo apt update
	   sudo apt install nvidia-docker2
	   sudo systemctl restart docker
	```
	
	1.5. Настройка NVIDIA runtime с containerd:
	
	```bash
	   sudo nvidia-ctk runtime configure --runtime=containerd
	```
	
	1.6. Перезапустк службы containerd с новыми настройками:
	
	```bash
	   sudo systemctl restart containerd
	```
	
	1.7. Повторить следующую команду:
	
	```bash
	sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
	```
	
### 2. Ошибка с нехваткой видеопамяти на компьютере:

***Tried to allocate 2.01 GiB (GPU 0; 3.78 GiB total capacity; 2.46 GiB already allocated; 481.06 MiB free; 2.49 GiB reserved in total by PyTorch) If reserved memory is >> allocated memory try setting max_split_size_mb to avoid fragmentation. See documentation for Memory Management and PYTORCH_CUDA_ALLOC_CONF***
	
	2.1. Останавливаем работающие контейнеры:
	
	```bash
	docker-compose down   
	```
	
	2.2. Удаляем контейнер:
	
	```bash
	docker rm vl_server   
	```
	
	2.3. Запуск контейнера с приложением на Streamlit с использованием docker-compose, с пересборкой образа, в фоновом режиме:
	
	```bash
	STREAMLIT=1 docker-compose up --build -d   
	```
	
### 3. Ошибка ModuleNotFoundError: No module named 'altait.vegalite.v4'

	3.1. Заходим в контейнер:
	
	```bash
	docker exec -it vl_server bash
	```
	
	3.2. Скачиваем в контейнер модуль:
	
	```bash
	pip install altair==4   
	```
	
	3.3. Выходим из контейнера:
	
	```bash
	exit   
	```
	
	3.4. Останавливаем контейнер:
	
	```bash
	docker stop vl_server   
	```
	
	3.5. Запускаем контейнеры:
	
	```bash
	STREAMLIT=1 docker-compose up -d   
	```
	
