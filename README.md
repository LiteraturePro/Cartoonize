# Cartoonizer 

> Convert images and videos into a cartoon!

The webapp is deployed Zeet.co here - https://cartoonize.zeet.app/cartoonize 

The webapp is deployed Heroku here - https://cartoonize-hk.herokuapp.com/

The webapp is deployed Aliyun Severless here - https://cartoonize.wxiou.cn/

---

## Update
[Use the associated applications of the model](https://github.com/LiteraturePro/Wx-Photo/)

## Contents

- [Prerequisites for Google Cloud and Algorithmia](#prerequisites-for-google-cloud-and-algorithmia)
- [Installation](#installation)
  - [Docker](#using-docker)
  - [Zeet.co](#using-zeet.co)
  - [Heroku](#using-heroku)
  - [Aliyun Severless](#using-aliyun-severless)
  - [VirtualEnv](#using-virtualenv)
  - [Google Colab](#using-google-colab)
- [API Version](#api-version)
- [Sample Image and Video](#sample-image-and-video)

---

## Prerequisites for Google Cloud and Algorithmia

**These are important steps if you want to leverage Google buckets, signed URLs and Algorithmia's platform. Skip this if you want to run locally / colab.**

### Cloud Run authentication
To use any functionalities pertaining to Google Cloud, you'll need a global authentication file (JSON). You can obtain this JSON by following the steps given here - [Getting started with authentication](https://cloud.google.com/docs/authentication/getting-started)

After you get the JSON file, rename it to `token.json` (so that it's compatible with the codebase). 

Set the environment variable in your terminal -
```
export GOOGLE_APPLICATION_CREDENTIALS="/path/to/token.json"
```
**Notes**:
- You can set it permanently by adding this line to `~/.bashrc`.
- `Dockerfile` already includes the setting of this particular environment variable. :)


### Algorithmia
We used the Serveless AI Layer product of [Algorithmia](https://algorithmia.com/serverless-ai-layer) for inference on videos.
To learn more on how to deploy your model in Algorithmia, check here - https://algorithmia.com/developers

---

## Installation

### Application tested on:

- python 3.7
- tensorflow 2.1.0 
- tf_slim 1.1.0
- ffmpeg 3.4.8
- Cuda version 10.1
- OS: Linux (Ubuntu 18.04)

### Using `Docker`

The easiest way to get the webapp running is by using the Dockerfile:

1. `cd` into the root directory and build the image
```
docker build -t cartoonize .
```
**Note**: Set the appropriate values in `config.yaml` before building the image.

2. Run the container by exposing the appropriate ports
```
docker run -p 8080:8080 cartoonize
```

### Using `Zeet.co`
- https://zeet.co/home
- CPU and GPU Build Success https://hub.docker.com/u/literature
1. Before starting, you need to log in to zeet and log in directly with your Github account.

2. Choose docker image
![](https://pcdn.wxiou.cn/20210201152104.png)

3. Choose a free plan.The free plan can only deploy one CPU container, the second one requires payment, the free plan only has CPU, and the paid plan has GPU.
![](https://pcdn.wxiou.cn/20210201152156.png)

4. You can go to the address(https://hub.docker.com/r/literature/cartoonize-cpu/tags), of course, you can also compile by yourself.
- The difference between CPU and CPU_15s is that CPU_15s can only process videos up to 15s, while cpu does not limit the length of the video.
![](https://pcdn.wxiou.cn/20210201152249.png)

5. Fill in the image name you want, Zeet.co will automatically pull it and complete the deployment.
- such as `literature/cartoonize-cpu:cpu`
![](https://pcdn.wxiou.cn/20210201152335.png)

6. You can get the deployment process in real time, and click `Visit Active Deployment` after completion Visit your application.
![](https://pcdn.wxiou.cn/20210201152414.png)

7. Zeet provides a free container for each user, please don’t abuse it and cherish resources.
![](https://pcdn.wxiou.cn/20210204131207.png)

### Using `Heroku`
- https://dashboard.heroku.com/apps
- There are some differences between the general docker image and the docker image used by Heroku, but I have compiled the image used by Heroku and can use it directly.
- CPU and GPU for Heroku Build Success https://hub.docker.com/u/literature

1. Create a new app, choose any name, but don’t use the same name as others
![](https://pcdn.wxiou.cn/20210204131557.png)

2. After entering the app, select `deploy`, and then select `Container Registry`, Pay attention to the following command, and `learn more` on the left, there are many commands you will use, if there is an error, you can check the command yourself
![](https://pcdn.wxiou.cn/20210204131709.png)

3. I use my own compiled image to deploy, you can use it, or you can compile one yourself.
- I use my own server for deployment. If you want to deploy a server, you need to make sure that your local network is consistent with the server network so that Heroku can verify your identity. There is no such problem when using local.
- Make sure your server environment or local environment has `Docker` and `Heroku CLI` installed, If not, please install it first. These two conditions determine whether it is successful. Give two links for reference:
- [Docker](https://docs.docker.com/get-docker/) 
- [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install) 
![](https://pcdn.wxiou.cn/20210204133112.png)

4. Pull the mirror to the local

```
docker pull literature/cartoonize-cpu:heroku
```

![](https://pcdn.wxiou.cn/20210204133307.png)

5. Log in to Heroku.If you use the server deployment, you will be given a link, click the link to verify, but the server network and your local network must be the same, if you use the local PC deployment, it will automatically jump, just click verify.

```
heroku login
```
![](https://pcdn.wxiou.cn/20210204133756.png)

6. Logging in to the registry. Heroku runs a container registry on `registry.heroku.com`.
```
heroku container:login
```
![](https://pcdn.wxiou.cn/20210204133943.png)

7. Pushing an existing image to your Heroku app.
- Official order

```
docker tag <image> registry.heroku.com/<app>/<process-type>
docker push registry.heroku.com/<app>/<process-type>
```

- Modify it like me, <image> is changed to `literature/cartoonize-cpu:heroku`, and app is changed to the name of the application we created in the first step. `<process-type>` is modified to the type you want to publish. I will modify it to `web` here. For more information, please refer to the official documentation.
  
```
docker tag literature/cartoonize-cpu:heroku registry.heroku.com/cartoonize-hk/web
docker push registry.heroku.com/cartoonize-hk/web
```
![](https://pcdn.wxiou.cn/20210204134701.png)
![](https://pcdn.wxiou.cn/20210204134732.png)

8. Now we push the image to the Heroku registry, and then load the warehouse image into the application.
- The parameter behind `-a` is your application name
```
heroku container:release web -a cartoonize-hk
```
![](https://pcdn.wxiou.cn/20210204135110.png)

9. Launch your app.
- The parameter behind `-a` is your application name
```
heroku open -a cartoonize-hk
```
![](https://pcdn.wxiou.cn/20210204135214.png)

- Copy the link to the browser to access

10. Heroku also provides free services to everyone, please don't abuse it.

### Using `Aliyun Severless`


### Using `virtualenv`

1. Make a virtual environment using `virutalenv` and activate it
```
virtualenv -p python3 cartoonize
source cartoonize/bin/activate
```
2. Install python dependencies
```
pip install -r requirements.txt
```
3. Run the webapp. Be sure to set the appropriate values in `config.yaml` file before running the application.
```
python app.py
```

### Using [Google Colab](https://colab.research.google.com/drive/1oDhMEVMcsRbe7bt-2A7cDsx44KQpQwuB?usp=sharing)
1. Clone the repository using either of the below mentioned way:
   - Using Command:
        - Create a new Notebook in Colab and in the cell execute the below command.  
        
        ```
         ! git clone https://github.com/experience-ml/cartoonize.git
        ```
        **Note:** Don't forget to add `!` at the beginning of the command
        
    - From Colab User Interface
 ```
        Open Colab
            └── File
                 └── Open Notebook
                          └── Github
                                └── paste the Url of the repository
 ```
 Note :  Before running the application change the runtime to GPU for processing videos but you for images CPU shall also work just fine.
 ```
            Runtime
               └── Change runtime type
                           └── Select GPU
 ```
2. After cloning the repository navigate to the `/cartoonize` using below command in the notebook cell:

   ```
   %cd cartoonize
   ```
3. Run the below commands in the notebook cell to install the requirements. 

   ```
   !pip install -r requirements.txt
   ```


4. In config.yaml file set: 

   ``` 
   colab-mode: true 
   ``` 
   
5. Launch the flask app on ngrok

   ```
   !python app.py
   ```

#### Note : Sample [Google Colab Notebook](https://colab.research.google.com/drive/1oDhMEVMcsRbe7bt-2A7cDsx44KQpQwuB?usp=sharing) for reference

---

## API Version
- Use the following docker command to deploy the server
```
docker pull literature/cartoonize-cpu:api
docker run -p 8080:8080 literature/cartoonize-cpu:api
```
or
```
docker pull literature/cartoonize-gpu:api
docker run -p 8080:8080 literature/cartoonize-gpu:api
```
- The service will run on your local port 8080 or other ports you specify. If you need domain name access, you can act as a proxy.
- provided that you have installed `docker`. After you deploy correctly, both `GET` and `POST` requests can be accessed. The actual display is as follows
  - `Interface`: `http://your domain/api` or `http://127.0.0.1:8080/api` can be accessed.
  - `Parameter`: image  `value`: a picture
  - `Return value`: the base64 data stream after processing the image

![](https://pcdn.wxiou.cn/20210218162534.png)
![](https://pcdn.wxiou.cn/20210218155900.png)



## Sample Image and Video

### Emma Watson Cartoonized
<img alt="Emma Watson Cartoonized" style="border-width:0" src="static/sample_images/twitter_image.png" />

### Youtube Video of Avenger's Bar Scene Cartoonized
[![Cartoonized version of Avenger's bar scene](http://img.youtube.com/vi/GqduSLcmhto/0.jpg)](http://www.youtube.com/watch?v=GqduSLcmhto "AVENGERS BAR SCENE [Cartoonized Version]")

---

## License

1. Copyright © Literature ([Demo webapp](https://cartoonize.zeet.app/cartoonize))

    - Authors: Literature.

    - Licensed under the [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode) 
    - Commercial application is prohibited by license


2. Copyright (C) Xinrui Wang, Jinze Yu. ([White box cartoonization](https://github.com/SystemErrorWang/White-box-Cartoonization))
    - All rights reserved. 
    - Licensed under the CC BY-NC-SA 4.0 
    - Also, Commercial application is prohibited license (https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode).
