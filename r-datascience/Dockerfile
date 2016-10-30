
FROM base-datascience

MAINTAINER Luca Minello <luca.minello@gmail.com>

USER $NB_USER

# R packages
# Pin r-base to a specific build number for https://github.com/jupyter/docker-stacks/issues/210#issuecomment-246081809
RUN conda config --add channels r && \
    conda install --quiet --yes \
    'r-base=3.3.1 1' \
    'r-irkernel=0.7*' \
    'r-plyr=1.8*' \
    'r-devtools=1.11*' \
    'r-dplyr=0.4*' \
    'r-ggplot2=2.1*' \
    'r-tidyr=0.5*' \
    'r-shiny=0.13*' \
    'r-rmarkdown=0.9*' \
    'r-forecast=7.1*' \
    'r-stringr=1.0*' \
    'r-rsqlite=1.0*' \
    'r-reshape2=1.4*' \
    'r-nycflights13=0.2*' \
    'r-caret=6.0*' \
    'r-rcurl=1.95*' \
    'r-crayon=1.3*' \
    'r-randomforest=4.6*' && conda clean -tipsy

RUN conda config --add channels r && \
    conda install --quiet --yes \
    'r-essentials=1.4.2'   && conda clean -tipsy

USER root

## Add RStudio binaries to PATH
ENV PATH /usr/lib/rstudio-server/bin/:$PATH

RUN VER=$(wget --no-check-certificate -qO- https://s3.amazonaws.com/rstudio-server/current.ver) \
  && wget -q http://download2.rstudio.org/rstudio-server-${VER}-amd64.deb \
  && dpkg -i rstudio-server-${VER}-amd64.deb \
  && rm rstudio-server-*-amd64.deb \
  && ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc /usr/local/bin \
  && ln -s /usr/lib/rstudio-server/bin/pandoc/pandoc-citeproc /usr/local/bin \
  && wget https://github.com/jgm/pandoc-templates/archive/1.15.0.6.tar.gz \
  && mkdir -p /opt/pandoc/templates && tar zxf 1.15.0.6.tar.gz \
  && cp -r pandoc-templates*/* /opt/pandoc/templates && rm -rf pandoc-templates* \
  && mkdir /root/.pandoc && ln -s /opt/pandoc/templates /root/.pandoc/templates \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/

RUN mkdir /etc/R

## Ensure that if both httr and httpuv are installed downstream, oauth 2.0 flows still work correctly.
RUN echo '\n\
\n# Configure httr to perform out-of-band authentication if HTTR_LOCALHOST \
\n# is not set since a redirect to localhost may not work depending upon \
\n# where this Docker container is running. \
\nif(is.na(Sys.getenv("HTTR_LOCALHOST", unset=NA))) { \
\n  options(httr_oob_default = TRUE) \
\n}' >> /etc/R/Rprofile.site

RUN echo "PATH=\"/usr/lib/rstudio-server/bin/:\${PATH}\"" >> /etc/R/Renviron.site


COPY run.sh /etc/services.d/rstudio/run

EXPOSE 8787
WORKDIR $HOME

#USER $NB_USER

#CMD ["R"]