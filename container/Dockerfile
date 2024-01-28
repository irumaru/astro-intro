FROM node:lts AS base
WORKDIR /app

# package.jsonとpackage-lock.jsonのみをここにコピーすることで、以下の`-deps`ステップがソースコードから独立していることが保証されます。
# したがって、ソースコードが変更されただけであれば、 `-deps`ステップはスキップされます。
COPY package.json package-lock.json ./

FROM base AS prod-deps
RUN npm install --production

FROM base AS build-deps
RUN npm install --production=false

FROM build-deps AS build
COPY . .
RUN npm run build

FROM base AS runtime
COPY --from=prod-deps /app/node_modules ./node_modules
COPY --from=build /app/dist ./dist

ENV HOST=0.0.0.0
ENV PORT=80
EXPOSE 80
CMD node ./dist/server/entry.mjs
