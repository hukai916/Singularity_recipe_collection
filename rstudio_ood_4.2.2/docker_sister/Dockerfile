FROM rocker/rstudio:4.2.2

RUN apt-get update -q && apt-get install -q -y libz-dev
RUN apt-get update -q && apt-get install -q -y libglpk-dev
RUN apt-get update -q && apt-get install -q -y liblzma-dev
RUN apt-get update -q && apt-get install -q -y libbz2-dev
RUN apt-get update -q && apt-get install -q -y libcairo2-dev
RUN apt-get update -q && apt-get install -q -y libxt-dev

#CMD ["/init"]
CMD ["bash"]
