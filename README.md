# Singularity RStudio Server

This repo contains a Singularity file that contains R 4.1 and RStudio 1.4.1717. It has several additional linux dependencies installed that are required for common bioinformatics packages (openssl, libproj, libbz2, etc). If you have others you'd like added, feel free to open a PR (or make your own fork and add whatever you need).

The Singularity image for this can be pulled via `singularity pull library://j-andrews7/default/rstudio:4.1.0`.

This was mostly configured to run on HPCs in interactive jobs where users likely don't have the appropriate permissions for RStudio server to work properly. This requires a number of bindings to be made to the image and a secure cookie file to be provided. The cookie file can be produced with:

```
# Only needs to be run once.
mkdir -p "$HOME/rstudio-tmp/tmp/rstudio-server"
uuidgen > "$HOME/rstudio-tmp/tmp/rstudio-server/secure-cookie-key"
chmod 0600 "$HOME/rstudio-tmp/tmp/rstudio-server/secure-cookie-key"
```

In general, you can launch a script similar to the following from within an interactive job on your respective HPC to get it running, and it will print the IP address and port the server is running on that you can pop into your browser:

```
#!/bin/sh

workdir=${HOME}/rstudio-tmp

mkdir -p -m 700 ${workdir}/run ${workdir}/tmp ${workdir}/var/lib/rstudio-server 
cat > ${workdir}/database.conf <<END
provider=sqlite
directory=/var/lib/rstudio-server
END

# Set R_LIBS_USER to a path specific to rocker/rstudio to avoid conflicts with
# personal libraries from any R installation in the host environment
cat > ${workdir}/rsession.sh <<END
#!/bin/sh
export R_LIBS_USER=${HOME}/R/rocker-rstudio/4.1
exec rsession "\${@}"
END

chmod +x ${workdir}/rsession.sh

export SINGULARITY_BIND="/research:/research,${workdir}/run:/run,${workdir}/tmp:/tmp,${workdir}/database.conf:/etc/rstudio/database.conf,${workdir}/rsession.sh:/etc/rstudio/rsession.sh,${workdir}/var/lib/rstudio-server:/var/lib/rstudio-server"

# Do not suspend idle sessions.
# Alternative to setting session-timeout-minutes=0 in /etc/rstudio/rsession.conf
# https://github.com/rstudio/rstudio/blob/v1.4.1106/src/cpp/server/ServerSessionManager.cpp#L126
export SINGULARITYENV_RSTUDIO_SESSION_TIMEOUT=0

# Get unused socket per https://unix.stackexchange.com/a/132524
# Tiny race condition between the python & singularity commands
readonly PORT=$(python -c 'import socket; s=socket.socket(); s.bind(("", 0)); print(s.getsockname()[1]); s.close()')
# Get node IP address.
readonly ADD=$(nslookup `hostname` | grep -i address | awk -F" " '{print $2}' | awk -F# '{print $1}' | tail -n 1)

cat 1>&2 <<END
"Running RStudio at $ADD:$PORT"
END

singularity exec --cleanenv rstudio_4.1.0.sif \
    rserver --www-port ${PORT} \
            --rsession-path=/etc/rstudio/rsession.sh
            --secure-cookie-key-file ${workdir}/tmp/rstudio-server/secure-cookie-key
```

## License

This repo is distributed under the GNU-GPL3 license. See the LICENSE file for more details.
