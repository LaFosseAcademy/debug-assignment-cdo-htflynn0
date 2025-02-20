FROM postgres
ENV POSTGRES_PASSWORD docker
ENV POSTGRES_DB tour-de-france
EXPOSE 5432
COPY cyclists.sql /docker-entrypoint-initdb.d/