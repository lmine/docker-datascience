FROM r-datascience

MAINTAINER Luca Minello <luca.minello@gmail.com>


USER $NB_USER

# Install Jupyter notebook
 RUN conda install --quiet --yes \
     'notebook' \
     'nb_conda' \
     && conda clean -tipsy

 RUN conda install --quiet --yes -c anaconda-nb-extensions \
     'nbbrowserpdf'\
     'ipyparallel' && \
     conda clean -tipsy

# #RUN conda install -c anaconda 

# Install JupyterHub to get the jupyterhub-singleuser startup script
RUN pip --no-cache-dir install 'jupyterhub==0.5'

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

# # Activate ipywidgets extension in the environment that runs the notebook server
RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix

# Install Python 2 packages
# Remove pyqt and qt pulled in for matplotlib since we're only ever going to
# use notebook-friendly backends in these images
RUN conda create --quiet --yes -p $CONDA_DIR/envs/python2 python=2.7 \
    'ipython' \
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
    'hdf5' \
    'h5py' \
    'sqlalchemy' \
    'pyzmq'  && \
    conda remove -n python2 --quiet --yes --force qt pyqt && \
    conda clean -tipsy

# RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix
RUN ipcluster nbextension enable
# Add shortcuts to distinguish pip for python2 and python3 envs
RUN ln -s $CONDA_DIR/envs/python2/bin/pip $CONDA_DIR/bin/pip2 
#&& \
    #ln -s $CONDA_DIR/envs/python3/bin/pip $CONDA_DIR/bin/pip

# Import matplotlib the first time to build the font cache.
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/
RUN MPLBACKEND=Agg $CONDA_DIR/envs/python2/bin/python -c "import matplotlib.pyplot"

# Configure ipython kernel to use matplotlib inline backend by default
RUN mkdir -p $HOME/.ipython/profile_default/startup
COPY mplimporthook.py $HOME/.ipython/profile_default/startup/

USER root

# Install Python 2 kernel spec globally to avoid permission problems when NB_UID
# switching at runtime.
RUN $CONDA_DIR/envs/python2/bin/python -m ipykernel install

EXPOSE 8888
WORKDIR $HOME

COPY run.sh /etc/services.d/jupyter/run

COPY start.sh /usr/local/bin/
COPY start-notebook.sh /usr/local/bin/
COPY start-singleuser.sh /usr/local/bin/
COPY jupyter_notebook_config.py /home/$NB_USER/.jupyter/

#USER $NB_USER

#CMD ["start-notebook.sh"]
#USER $NB_USER