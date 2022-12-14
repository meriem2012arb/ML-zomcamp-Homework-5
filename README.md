# ML-zomcamp-Homework-5

## Homework

In this homework, we will use Credit Card Data from [the previous homework](https://github.com/alexeygrigorev/mlbookcamp-code/blob/master/course-zoomcamp/cohorts/2022/04-evaluation/homework.md).

## Question 1

* Install Pipenv
* What's the version of pipenv you installed?

Answer : 2022.10.10


## Question 2

* Use Pipenv to install Scikit-Learn version 1.0.2
* What's the first hash for scikit-learn you get in Pipfile.lock?

Answer : 62db916eaa3ba201789358b59e73eb7630266ef79c9c3f4d67236779aaf5f04a


## Models

We've prepared a dictionary vectorizer and a model.

They were trained (roughly) using this code:

```python
features = ['reports', 'share', 'expenditure', 'owner']
dicts = df[features].to_dict(orient='records')

dv = DictVectorizer(sparse=False)
X = dv.fit_transform(dicts)

model = LogisticRegression(solver='liblinear').fit(X, y)
```

> **Note**: You don't need to train the model. This code is just for your reference.

And then saved with Pickle. Download them:

* [DictVectorizer](https://github.com/alexeygrigorev/mlbookcamp-code/blob/master/course-zoomcamp/cohorts/2022/05-deployment/homework/dv.bin?raw=true)
* [LogisticRegression](https://github.com/alexeygrigorev/mlbookcamp-code/blob/master/course-zoomcamp/cohorts/2022/05-deployment/homework/model1.bin?raw=true)

With `wget`:

```bash
PREFIX=https://raw.githubusercontent.com/alexeygrigorev/mlbookcamp-code/master/course-zoomcamp/cohorts/2022/05-deployment/homework
wget $PREFIX/model1.bin
wget $PREFIX/dv.bin
```


## Question 3

Let's use these models!

* Write a script for loading these models with pickle
* Score this client:

```json
{"reports": 0, "share": 0.001694, "expenditure": 0.12, "owner": "yes"}
```

What's the probability that this client will get a credit card? 

* 0.162
* 0.391
* 0.601
* 0.993

Answer : 0.162



## Question 4

Now let's serve this model as a web service

* Install Flask and gunicorn (or waitress, if you're on Windows)
* Write Flask code for serving the model
* Now score this client using `requests`:

```python
url = "YOUR_URL"
client = {"reports": 0, "share": 0.245, "expenditure": 3.438, "owner": "yes"}
requests.post(url, json=client).json()
```

What's the probability that this client will get a credit card?

* 0.274
* 0.484
* 0.698
* 0.928


Answer : 0.928


## Docker

Install [Docker](https://github.com/alexeygrigorev/mlbookcamp-code/blob/master/course-zoomcamp/05-deployment/06-docker.md). We will use it for the next two questions.

For these questions, we prepared a base image: `svizor/zoomcamp-model:3.9.12-slim`. 
You'll need to use it (see Question 5 for an example).

This image is based on `python:3.9.12-slim` and has a logistic regression model 
(a different one) as well a dictionary vectorizer inside. 

This is how the Dockerfile for this image looks like:

```docker 
FROM python:3.9.12-slim
WORKDIR /app
COPY ["model2.bin", "dv.bin", "./"]
```

We already built it and then pushed it to [`svizor/zoomcamp-model:3.9.12-slim`](https://hub.docker.com/r/svizor/zoomcamp-model).


## Question 5

Download the base image `svizor/zoomcamp-model:3.9.12-slim`. You can easily make it by using [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) command.

So what's the size of this base image?

* 15 Mb
* 125 Mb
* 275 Mb
* 415 Mb
* 

Answer: 125 Mb


You can get this information when running `docker images` - it'll be in the "SIZE" column.


## Dockerfile

Now create your own Dockerfile based on the image we prepared.

It should start like that:

```docker
FROM svizor/zoomcamp-model:3.9.12-slim
# add your stuff here
```

Now complete it:

* Install all the dependencies form the Pipenv file
* Copy your Flask script
* Run it with Gunicorn 

After that, you can build your docker image.


## Question 6

Let's run your docker container!

After running it, score this client once again:

```python
url = "YOUR_URL"
client = {"reports": 0, "share": 0.245, "expenditure": 3.438, "owner": "yes"}
requests.post(url, json=client).json()
```

What's the probability that this client will get a credit card now?

* 0.289
* 0.502
* 0.769
* 0.972

Answer : 0.769
