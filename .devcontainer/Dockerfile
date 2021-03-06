# [Choice] Node.js version: https://hub.docker.com/r/arm64v8/node/
ARG VARIANT="15-buster"
FROM arm64v8/node:${VARIANT}

# Install needed packages, yarn, nvm and setup non-root user. Use a separate RUN statement to add your own dependencies.
ARG USERNAME=node
ARG USER_UID=1000
ARG USER_GID=$USER_UID
ARG NPM_GLOBAL=/usr/local/share/npm-global
ENV NVM_DIR=/usr/local/share/nvm
ENV NVM_SYMLINK_CURRENT=true \ 
    PATH=${NPM_GLOBAL}/bin:${NVM_DIR}/current/bin:${PATH}
COPY library-scripts/*.sh /tmp/library-scripts/
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive
# Remove imagemagick due to https://security-tracker.debian.org/tracker/CVE-2019-10131
RUN apt-get purge -y imagemagick imagemagick-6-common
# Install common packages, non-root user, update yarn and install nvm
RUN bash /tmp/library-scripts/common-debian.sh "${INSTALL_ZSH}" "${USERNAME}" "${USER_UID}" "${USER_GID}" "${UPGRADE_PACKAGES}"
# Install yarn, nvm
RUN rm -rf /opt/yarn-* /usr/local/bin/yarn /usr/local/bin/yarnpkg
RUN bash /tmp/library-scripts/node-debian.sh "${NVM_DIR}" "none" "${USERNAME}"
# Configure global npm install location, use group to adapt to UID/GID changes
RUN if ! cat /etc/group | grep -e "^npm:" > /dev/null 2>&1; then groupadd -r npm; fi
RUN usermod -a -G npm ${USERNAME}
RUN umask 0002
RUN mkdir -p ${NPM_GLOBAL}
RUN chown ${USERNAME}:npm ${NPM_GLOBAL}
RUN chmod g+s ${NPM_GLOBAL}
RUN npm config -g set prefix ${NPM_GLOBAL}
# RUN sudo -u ${USERNAME} npm config -g set prefix ${NPM_GLOBAL}
# RUN npm cache clean --force > /dev/null 2>&1
# Clean up
RUN apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* /root/.gnupg /tmp/library-scripts

# [Optional] Uncomment this section to install additional OS packages.
# RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
# RUN apt-get -y install --no-install-recommends <your-package-list-here>

# [Optional] Uncomment if you want to install an additional version of node using nvm
# ARG EXTRA_NODE_VERSION=10
# RUN su node -c "source /usr/local/share/nvm/nvm.sh && nvm install ${EXTRA_NODE_VERSION}"

# [Optional] Uncomment if you want to install more global node modules
# RUN su node -c "npm install -g <your-package-list-here>""