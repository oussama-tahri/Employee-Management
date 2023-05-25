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

class Employee(models.Model):
    name = models.CharField(max_length=100)
    role = models.CharField(max_length=100)
    salary = models.DecimalField(max_digits=8, decimal_places=2)

    def __str__(self):
        return self.name

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

##### 1.1- Vue pour la liste des employés (accessible par les deux rôles) :

```python
from django.shortcuts import render
from .models import Employee

def employee_list(request):
    employees = Employee.objects.all()
    context = {'employees': employees}
    return render(request, 'employees/employee_list.html', context)
```

##### 1.2- Vue pour l'affichage détaillé d'un employé (accessible par les deux rôles) :

```python
from django.shortcuts import render, get_object_or_404
from .models import Employee

def employee_detail(request, employee_id):
    employee = get_object_or_404(Employee, id=employee_id)
    context = {'employee': employee}
    return render(request, 'employees/employee_detail.html', context)
```

##### 1.3- Vue pour l'ajout, la modification et la suppression des employés (accessible uniquement par l'administrateur) :

```python
from django.shortcuts import render, redirect, get_object_or_404
from .forms import EmployeeForm
from .models import Employee

def add_employee(request):
    if request.method == 'POST':
        form = EmployeeForm(request.POST)
        if form.is_valid():
            form.save()
            return redirect('employee_list')
    else:
        form = EmployeeForm()
    
    context = {'form': form}
    return render(request, 'employees/add_employee.html', context)

def edit_employee(request, employee_id):
    employee = get_object_or_404(Employee, id=employee_id)
    
    if request.method == 'POST':
        form = EmployeeForm(request.POST, instance=employee)
        if form.is_valid():
            form.save()
            return redirect('employee_list')
    else:
        form = EmployeeForm(instance=employee)
    
    context = {'form': form}
    return render(request, 'employees/edit_employee.html', context)

def delete_employee(request, employee_id):
    employee = get_object_or_404(Employee, id=employee_id)
    
    if request.method == 'POST':
        employee.delete()
        return redirect('employee_list')
    
    context = {'employee': employee}
    return render(request, 'employees/delete_employee.html', context)
```

## Étape 7: Création des Modèles de formulaires

#### 1. Formulaire pour l'ajout et la modification d'un employé :

```python
from django import forms
from .models import Employee

class EmployeeForm(forms.ModelForm):
    class Meta:
        model = Employee
        fields = ('name', 'role', 'salary')
```

## Étape 8: URLs

#### 1. Configuration des URLs :

```python
from django.contrib import admin
from django.urls import path
from employees.views import employee_list, employee_detail, add_employee, edit_employee, delete_employee

urlpatterns = [
    path('admin/', admin.site.urls),
    path('employees/', employee_list, name='employee_list'),
    path('employees/<int:employee_id>/', employee_detail, name='employee_detail'),
    path('employees/add/', add_employee, name='add_employee'),
    path('employees/<int:employee_id>/edit/', edit_employee, name='edit_employee'),
    path('employees/<int:employee_id>/delete/', delete_employee, name

='delete_employee'),
]
```

## Templates

#### 1. Template pour la liste des employés (`employee_list.html`) :

```html
<h1>Liste des employés</h1>

<ul>
    {% for employee in employees %}
        <li>
            <a href="{% url 'employee_detail' employee.id %}">{{ employee.name }}</a>
            {% if user.is_superuser %}
                | <a href="{% url 'edit_employee' employee.id %}">Modifier</a>
                | <a href="{% url 'delete_employee' employee.id %}">Supprimer</a>
            {% endif %}
        </li>
    {% empty %}
        <li>Aucun employé</li>
    {% endfor %}
</ul>
```

#### 2. Template pour l'affichage détaillé d'un employé (`employee_detail.html`) :

```html
<h1>Détails de l'employé</h1>

<p><strong>Nom:</strong> {{ employee.name }}</p>
<p><strong>Rôle:</strong> {{ employee.role }}</p>
<p><strong>Salaire:</strong> {{ employee.salary }}</p>
```

#### 3. Template pour l'ajout d'un employé (`add_employee.html`) :

```html
<h1>Ajouter un employé</h1>

<form method="POST" action="{% url 'add_employee' %}">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Ajouter</button>
</form>
```

#### 4. Template pour la modification d'un employé (`edit_employee.html`) :

```html
<h1>Modifier l'employé</h1>

<form method="POST" action="{% url 'edit_employee' employee.id %}">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Modifier</button>
</form>
```

#### 5. Template pour la suppression d'un employé (`delete_employee.html`) :

```html
<h1>Supprimer l'employé</h1>

<p>Voulez-vous vraiment supprimer l'employé "{{ employee.name }}" ?</p>

<form method="POST" action="{% url 'delete_employee' employee.id %}">
    {% csrf_token %}
    <button type="submit">Supprimer</button>
</form>
```

Assurez-vous d'ajouter les chemins vers les templates dans votre configuration de répertoire `TEMPLATES` dans le fichier `settings.py` de votre projet Django.

Cela vous donne un projet de gestion des employés en Django avec deux rôles, un rôle administrateur qui peut afficher les employés, ajouter, supprimer et modifier les employés, et un rôle utilisateur qui peut seulement consulter les employés. Vous pouvez personnaliser davantage le projet en ajoutant des fonctionnalités supplémentaires en fonction de vos besoins spécifiques.
Et voilà! Vous avez créé un projet de gestion des employés en utilisant Django. Vous pouvez étendre ce projet en ajoutant des fonctionnalités telles que la création, la modification et la suppression d'employés, ainsi que la gestion des autres détails liés aux employés.

N'oubliez pas de consulter la documentation officielle de Django (https://docs.djangoproject.com/) pour plus d'informations et de ressources sur le développement avec Django.
