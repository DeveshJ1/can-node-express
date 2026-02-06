FROM node:lts-bookworm-slim AS base
RUN apt-get update && apt-get install -y libssl-dev ca-certificates 
WORKDIR /app
COPY package.json yarn.lock ./
FROM base AS build
RUN export NODE_ENV=production
RUN yarn
COPY . .
RUN npx prisma generate
RUN yarn build
FROM base AS prod-build
RUN yarn install --production
COPY prisma prisma
RUN npx prisma generate
RUN cp -R node_modules prod_node_modules
FROM base AS prod
COPY --from=prod-build /app/prod_node_modules /app/node_modules
COPY --from=build /app/.next /app/.next
COPY --from=build /app/public /app/public
COPY --from=build /app/prisma /app/prisma
EXPOSE 3000
CMD ["yarn","start"]
