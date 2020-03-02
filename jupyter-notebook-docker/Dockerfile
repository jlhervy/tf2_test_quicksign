FROM jupyter/base-notebook



RUN pip install --upgrade pip
RUN pip --no-cache-dir install poetry

WORKDIR /home/jovyan/work

COPY ./pyproject.toml .

RUN poetry config virtualenvs.create false
RUN poetry install --no-dev -vvv





