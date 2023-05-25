<h1 align="center">Employee-Management</h1>
<br>

## Étape 1: Configuration de l'environnement

Assurez-vous d'avoir Python et Django installés sur votre machine.

#### 1. Installez Python en téléchargeant le package approprié pour votre système d'exploitation depuis le site officiel de Python
(https://www.python.org)
#### 2. Installez Django en exécutant la commande suivante dans votre terminal:

   ```
   pip install Django
   ```

## Étape 2: Création d'un nouveau projet Django

#### 1. Dans votre terminal, accédez au répertoire où vous souhaitez créer votre projet.
#### 2. Exécutez la commande suivante pour créer un nouveau projet Django:

   ```
   django-admin startproject gestion_employes
   ```

## Étape 3: Création de l'application "employes"

#### 1. Accédez au répertoire du projet en exécutant la commande suivante:

   ```
   cd gestion_employes
   ```

#### 2. Créez une nouvelle application Django appelée "employes" en exécutant la commande suivante:

   ```
   python manage.py startapp employes
   ```

#### 3. Ouvrez le fichier `settings.py` situé dans le répertoire `gestion_employes` et ajoutez `'employes'` à la liste `INSTALLED_APPS`:

   ```python
   INSTALLED_APPS = [
       ...
       'employes',
   ]
   ```

## Étape 4: Définition du modèle "Employe"

#### 1. Ouvrez le fichier `models.py` situé dans le répertoire `employes`.
#### 2. Remplacez le contenu par le code suivant pour définir le modèle `Employe`:

   ```python
   from django.db import models

   class Employe(models.Model):
       nom = models.CharField(max_length=100)
       poste = models.CharField(max_length=100)
       date_embauche = models.DateField()
       salaire = models.DecimalField(max_digits=8, decimal_places=2)

       def __str__(self):
           return self.nom
   ```

#### 3. Enregistrez le fichier.

## Étape 5: Appliquer les migrations

#### 1. Exécutez la commande suivante pour créer les tables de base de données nécessaires:

   ```
   python manage.py makemigrations
   python manage.py migrate
   ```

## Étape 6: Création des vues

#### 1. Ouvrez le fichier `views.py` situé dans le répertoire `employes`.
#### 2. Remplacez le contenu par le code suivant pour définir les vues:

   ```python
   from django.shortcuts import render
   from .models import Employe

   def liste_employes(request):
       employes = Employe.objects.all()
       context = {'employes': employes}
       return render(request, 'employes/liste_employes.html', context)
   ```

## Étape 7: Création des templates

#### 1. Créez un répertoire appelé `templates` dans le répertoire `employes`.
#### 2. À l'intérieur du répertoire `templates`, créez un autre répertoire appelé `employes`.
#### 3. À l'intérieur du répertoire `employes`, créez un fichier appelé `liste_employes.html`.
#### 4. Dans le fichier `liste_employes.html`, ajoutez le code

 suivant pour afficher la liste des employés:

   ```html
   <h1>Liste des employés</h1>

   <ul>
       {% for employe in employes %}
           <li>{{ employe.nom }}</li>
       {% empty %}
           <li>Aucun employé</li>
       {% endfor %}
   </ul>
   ```

## Étape 8: Configuration des URLs


#### 1. Ouvrez le fichier `urls.py` situé dans le répertoire `gestion_employes`.
#### 2. Remplacez le contenu par le code suivant pour configurer les URLs:

   ```python
   from django.contrib import admin
   from django.urls import path
   from employes.views import liste_employes

   urlpatterns = [
       path('admin/', admin.site.urls),
       path('employes/', liste_employes, name='liste_employes'),
   ]
   ```

## Étape 9: Exécution du serveur de développement

#### 1. Dans votre terminal, exécutez la commande suivante pour démarrer le serveur de développement Django:

   ```
   python manage.py runserver
   ```

#### 2. Ouvrez votre navigateur et accédez à l'URL `http://localhost:8000/employes/` pour voir la liste des employés.

Et voilà! Vous avez créé un projet de gestion des employés en utilisant Django. Vous pouvez étendre ce projet en ajoutant des fonctionnalités telles que la création, la modification et la suppression d'employés, ainsi que la gestion des autres détails liés aux employés.

N'oubliez pas de consulter la documentation officielle de Django (https://docs.djangoproject.com/) pour plus d'informations et de ressources sur le développement avec Django.
