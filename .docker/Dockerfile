
# Etapa de build
FROM node:22.19-alpine3.22 AS builder
WORKDIR /app

# Copia os arquivos de dependências
COPY package.json package-lock.json* ./

# Instala dependências (inclui dev para build)
RUN npm ci --ignore-scripts

# Copia o restante do código
COPY . ./

# Compila o projeto (NestJS)
RUN npm run build

# Etapa de produção
FROM node:22.19-alpine3.22 AS production
WORKDIR /app

# Copia apenas arquivos necessários da etapa de build
COPY --from=builder /app/package.json ./
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist

# Define variáveis de ambiente
ENV NODE_ENV=production

# Usa usuário não-root
USER node

# Porta padrão NestJS
EXPOSE 3000

# Comando de inicialização
CMD ["node", "dist/main.js"]

