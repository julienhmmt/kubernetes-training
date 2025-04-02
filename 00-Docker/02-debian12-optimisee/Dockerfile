# Image de base
FROM debian:12

# Installation de Python 3 et pip
RUN apt-get update
RUN apt-get install -y python3 python3-pip

# Copie de l’application dans le conteneur
WORKDIR /app
COPY app/ /app/

# Installation des dépendances Python
RUN pip3 install --break-system-packages -r requirements.txt

# Exposition du port de l’API
EXPOSE 8000

# Commande de démarrage de l’API
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
