FROM r-datascience

MAINTAINER Luca Minello <luca.minello@gmail.com>

USER $NB_USER

# Install Jupyter notebook
 RUN conda install --quiet --yes \
     'notebook' \
     'nb_conda' \
    && conda clean -tipsy

RUN conda install --quiet --yes -c anaconda-nb-extensions \
     'nbbrowserpdf' \
     'ipyparallel' \
     && conda clean -tipsy

# Install JupyterHub to get the jupyterhub-singleuser startup script
RUN pip --no-cache-dir install 'jupyterhub==0.5'

# # Activate ipywidgets extension in the environment that runs the notebook server
RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix

# RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix
RUN ipcluster nbextension enable

# Install Python 3 packages
# Remove pyqt and qt pulled in for matplotlib since we're only ever going to
# use notebook-friendly backends in these images
RUN conda install --quiet --yes \
    'ipywidgets' \
    'pandas' \
    'numexpr' \
    'matplotlib' \
    'scipy' \
    'seaborn' \
    'scikit-learn' \
    'scikit-image' \
    'sympy' \
    'cython' \
    'patsy' \
    'statsmodels' \
    'cloudpickle' \
    'dill' \
    'numba' \
    'bokeh' \
    'sqlalchemy' \
    'hdf5' \
    'h5py'  && \
    conda remove --quiet --yes --force qt pyqt && \
    conda clean -tipsy

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/

# Configure ipython kernel to use matplotlib inline backend by default
RUN mkdir -p $HOME/.ipython/profile_default/startup
COPY mplimporthook.py $HOME/.ipython/profile_default/startup/

USER root

EXPOSE 8888
WORKDIR $HOME

COPY run.sh /etc/services.d/jupyter/run

COPY start.sh /usr/local/bin/
COPY start-notebook.sh /usr/local/bin/
COPY start-singleuser.sh /usr/local/bin/
COPY jupyter_notebook_config.py /home/$NB_USER/.jupyter/