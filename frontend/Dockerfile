FROM node:22.14.0
WORKDIR /app
COPY frontend/package*.json ./
RUN npm install
COPY frontend ./
EXPOSE 5173
CMD ["npm", "run", "dev"]