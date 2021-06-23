BootStrap: docker
From: rocker/rstudio:4.0.5

%runscript
  exec rserver "${@}"

%post
  apt-get update
  apt-get install -y --no-install-recommends \
    locales \
    aptitude

  # Configure default locale
  echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
  locale-gen en_US.utf8
  /usr/sbin/update-locale LANG=en_US.UTF-8
  export LC_ALL=en_US.UTF-8
  export LANG=en_US.UTF-8

  # Install dependencies.
  apt-get update
  aptitude install -y \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    libcairo2-dev \
    libxt-dev \
    libpng-dev \
    libfreetype6-dev \
    libtiff5-dev \
    libjpeg-dev \
    libgsl-dev \
    librsvg2-dev \
    libnode-dev \
    libv8-dev \
    software-properties-common \
    libharfbuzz-dev \
    librsvg2-dev \
    libproj-dev \
    libbz2-dev

  # Disable session timeout
  echo "session-timeout-minutes=0" > /etc/rstudio/rsession.conf
