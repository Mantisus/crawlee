# using multistage build, as we need dev deps to build the TS source code
FROM apify/actor-node:20-beta AS builder

# copy all files, install all dependencies (including dev deps) and build the project
COPY . ./
RUN npm install --include=dev \
    && npm run build

# create final image
FROM apify/actor-node:20-beta
# copy only necessary files
COPY --from=builder /usr/src/app/packages ./packages
COPY --from=builder /usr/src/app/package.json ./
COPY --from=builder /usr/src/app/main.js ./

# install only prod deps
RUN npm --quiet set progress=false \
    && npm install --only=prod --no-audit \
    && npm update --no-audit \
    && echo "Installed NPM packages:" \
    && (npm list --omit=dev --all || true) \
    && echo "Node.js version:" \
    && node --version \
    && echo "NPM version:" \
    && npm --version

# run compiled code
CMD npm run start:prod
