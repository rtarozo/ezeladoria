# More Datascience frendly gist can be found here: https://gist.github.com/ruddra/870d7a51238ddfa4b50375086c12a4f5
# pull official python alpine image
FROM alpine:3.18

# set environment variables
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
ENV APP_HOME /home/django/app

# Creating Work Directory
RUN mkdir -p $APP_HOME
WORKDIR $APP_HOME
RUN mkdir static media


# Adding mandatory packages to docker
RUN apk update && apk add --no-cache \
    postgresql \
    zlib \
    jpeg 
# un-comment the following two dependecies if you want to add library like pandas, scipy and numpy
# openblas \ 
# libstdc++ 

# Installing temporary packages required for installing requirements.pip 
RUN apk add --no-cache --virtual build-deps \
    gcc \  
    python3-dev \ 
    musl-dev \
    postgresql-dev\
    zlib-dev \
    jpeg-dev \
# un-comment if you want to install numpy, pandas, scipy etc and their supported dependencies
# g++ \
# openblas-dev \
# cmake \
# && ln -s /usr/include/locale.h /usr/include/xlocale.h

# Update pip
RUN apk add --update py3-pip
RUN pip install --upgrade pip

# create the app user
RUN addgroup -S django && adduser -S django -G django

# **if you want to install scipy uncomment the following file**
#RUN pip3 install --no-cache-dir --disable-pip-version-check scipy==1.3.1

# Installing requirements.pip from project
COPY ./requirements.txt ./
RUN pip install --no-cache-dir -r ./requirements.txt

# Copy project to container
COPY ./src ./

# *install psycopg2 if you don't have it requirements.pip*
# RUN pip install --no-cache-dir psycopg2

# removing temporary packages from docker and removing cache 
RUN apk del build-deps && \
    find -type d -name __pycache__ -prune -exec rm -rf {} \; && \
    rm -rf ~/.cache/pip

# Give django user permissions
RUN chown -R django:django /home/django

# Change User
USER django

# CMD will run when this dockerfile is running
CMD ["sh", "-c", "python manage.py collectstatic --no-input; python manage.py makemigrations; python manage.py migrate; gunicorn core.wsgi -b 0.0.0.0:8000 & celery -A core worker -l info"]
