FROM node
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 8080
RUN ln -s /usr/bin/nodejs /usr/bin/node
CMD [“npm” ”server.js”]