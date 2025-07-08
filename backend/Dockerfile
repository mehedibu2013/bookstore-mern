FROM node:22.14.0
WORKDIR /app
COPY backend/package*.json ./
RUN npm install
COPY backend ./
EXPOSE 5555
CMD ["node", "index.js"]