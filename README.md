## Change notes

In this fork, changes have been made to run the Singularity image `rstudio_r_4.0.5-1.2004.0.def` on the [ISD HPC Cluster](http://cluster.isd.med.uni-muenchen.de). So no real feature improvements. Credits to the original author [@j-andrews7](https://github.com/j-andrews7).

```
  ___  ____   ____    _   _  ____    ____         _              _              
 |_ _|/ ___| |  _ \  | | | ||  _ \  / ___|   ___ | | _   _  ___ | |_  ___  _ __ 
  | | \___ \ | | | | | |_| || |_) || |      / __|| || | | |/ __|| __|/ _ \| '__|
  | |  ___) || |_| | |  _  ||  __/ | |___  | (__ | || |_| |\__ \| |_|  __/| |   
 |___||____/ |____/  |_| |_||_|     \____|  \___||_| \__,_||___/ \__|\___||_|   
                        cluster.isd.med.uni-muenchen.de  
```

-----

# Singularity RStudio Server

This repo contains Singularity files that contains specific R versions and RStudio. Each also has several additional linux dependencies installed that are required for common bioinformatics packages (openssl, libproj, libbz2, etc). If you have others you'd like added, feel free to open a PR (or make your own fork and add whatever you need).

The Singularity image for this can be pulled via `singularity pull library://j-andrews7/rstudio/rstudio:x.x.x`, where `x.x.x` is replaced by your R version of interest.

This was mostly configured to run on HPCs in interactive jobs where users likely don't have the appropriate permissions for RStudio server to work properly. This requires a number of bindings to be made to the image and a secure cookie file to be provided. The cookie file can be produced with:

```bash
# Only needs to be run once.
mkdir -p "$HOME/rstudio-tmp/tmp/rstudio-server"
uuidgen > "$HOME/rstudio-tmp/tmp/rstudio-server/secure-cookie-key"
chmod 0600 "$HOME/rstudio-tmp/tmp/rstudio-server/secure-cookie-key"
```

In general, you can launch a script similar to those provided (`rstudio_launch.x.x.x.sh`) from within an interactive job on your respective HPC to get it running, and it will print the IP address and port the server is running on that you can pop into your browser. The provided launch scripts will install/look for R packages in `${HOME}/R/rstudio/x.x.x`, where `x.x.x` corresponds to the R version. This means multiple R versions can be run without the package versions conflicting.

## License

This repo is distributed under the GNU-GPL3 license. See the LICENSE file for more details.
