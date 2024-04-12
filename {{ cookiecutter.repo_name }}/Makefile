SHELL := /bin/bash
ENV_NAME := {{ cookiecutter.env_name }}
ENV_MANAGER := {{ cookiecutter.env_manager }}
PYTHON_VERSION := {{ cookiecutter.python_version }}

{%- if cookiecutter.env_manager == "pyenv" %}
VENV_DIR := $(shell pyenv root)/versions/$(ENV_NAME)
{%- endif %}

{%- if cookiecutter.env_manager == "conda" %}
CONDA_ACTIVATE := source $$(conda info --base)/etc/profile.d/conda.sh ; conda activate ; conda activate
{%- endif %}

default_target: all

.PHONY: all
all: setup-env install-dependencies

.PHONY: setup-env
env:
    {%- if cookiecutter.env_manager == "pyenv" %}
	@echo "Creating the Pyenv environment..."
	@if [ -d "$(VENV_DIR)" ]; then \
		echo "Environment $(ENV_NAME) already exists. Please remove it before running this script."; \
	else \
		pyenv install --skip-existing $(PYTHON_VERSION); \
		pyenv virtualenv $(PYTHON_VERSION) $(ENV_NAME); \
		echo "Environment $(ENV_NAME) created. Please activate the environment by running 'pyenv activate $(ENV_NAME)'."; \
	fi
	{%- if cookiecutter.use_poetry == "y" %}
	source "$(VENV_DIR)/bin/activate"; \
	pip install poetry ; \
	poetry init --no-interaction
	{%- endif %}
	@echo "Environment $(ENV_NAME) created. Please activate the environment by running 'pyenv activate $(ENV_NAME)'."
	{%- endif %}
	{%- if cookiecutter.env_manager == "conda" %}
	@echo "Creating the Conda environment..."
	conda create --name $(ENV_NAME) python=$(PYTHON_VERSION) -y
	{%- if cookiecutter.use_poetry == "y" %}
	$(CONDA_ACTIVATE) $(ENV_NAME); \
	pip install poetry; \
	poetry init --no-interaction
	{%- endif %}
	@echo "Environment $(ENV_NAME) created. Please activate the environment by running 'conda activate $(ENV_NAME)'."
	{%- endif %}

.PHONY: install-dependencies
install-dependencies:
    {%- if cookiecutter.env_manager == "pyenv" %}
	@echo "Installing dependencies using Pyenv..."
	pyenv activate $(ENV_NAME); \
	{%- elif cookiecutter.env_manager == "conda" %}
	@echo "Installing dependencies using Conda..."
	$(CONDA_ACTIVATE) $(ENV_NAME); \
	{%- endif %}
	{%- if cookiecutter.use_poetry == "y" %}
	poetry install --no-root
	{%- else -%}
	pip install -r requirements.txt
	{%- endif %}

.PHONY: clean
clean:
	{%- if cookiecutter.env_manager == "conda" %}
	@echo "Removing the Conda environment..."
	conda env remove --name $(ENV_NAME)
	@echo "Environment $(ENV_NAME) has been removed."
	{%- elif cookiecutter.env_manager == "pyenv" %}
	@echo "Removing the Pyenv environment..."
	pyenv virtualenv-delete -f $(ENV_NAME)
	@echo "Environment $(ENV_NAME) has been removed."
	{%- else %}
	@echo "Environment manager not supported."
	{%- endif %}
