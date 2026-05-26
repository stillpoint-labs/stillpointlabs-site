FROM devcontainer-base:local

# npm global prefix — persisted in the stillpointlabs_site_npm named volume so packages
# survive image rebuilds. Matches the volume mount point in docker-compose.yml.
ENV NPM_CONFIG_PREFIX=/home/dev/.npm-global
ENV PATH=/home/dev/.npm-global/bin:$PATH

WORKDIR /workspaces/stillpointlabs-site
