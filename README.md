# Python-TDD-Hashing
Hashing portal using django and implementeded using TDD



Dont' write any code until you have a failing test
Write code to pass the test

Selenium, establecer un ambiente virtual https://www.liquidweb.com/kb/how-to-setup-a-python-virtual-environment-on-windows-10/

	1-pip install virtualenv
	2-virtualenv venv
	3-cd venv/Scripts
	4-Ejecutar activate.bat
		en linux o mac source venv(bin/activate
	5-pip install selenium
	6-We are going use firefox as browser
	7-desde firefox abrir https://github.com/mozilla/geckodriver/releases
	8-descargar  geckodriver-v0.27.0-win64.zip
	9-descomprimir dejar el exe en la carpeta venv/Scripts
	10-Crear un archivo func_test.py con lo siguiente
		from selenium import webdriver
		browser = webdriver.Firefox()
		browser.get('http://localhost:8000')
		assert browser.page_source.find("install")	
		
		Primer prueba en selenium va abrir el home de django y buscar si esta la palabra install
		
	11-Desde el ambiente virtual	
		pip install django
	12-Crear proyecto en django llamado hashthat
		django-admin startproject hashthat
	13-Iniciar el servidor
		python manage.py runserver
	14-Nueva consola
		call c:/Users/ovelez/venv/scripts/activate.bat
		en linux o mac source venv(bin/activate
    15-Ejecutar nuevamente la prueba	
		python fun_tests.py
		
		si no retorna nada por consola es que funcionó
		
	16-Django testing framework
		Crear una nueva app dentro de la carpeta del proyecto
		django-admin startapp hashing
		
	17-Copiar el contenido de func_tests.py en el archivo test.py del proyecto hashthat	app hashing	
	
	18-En el archivo tests.py crear
		class FunctionalTestCase(TestCase):
			def setUp(self):
				self.browser = webdriver.Firefox()

			def test_there_is_homepage(self):
				self.browser.get('http://localhost:8000')
				self.assertIn('install', self.browser.page_source)

			def tearDown(self):
				self.browser.quit()
				
	19-Ejecutar las pruebas python manage.py test		
		Con el framework de pruebas de django tenemos más información
			Ran 1 test in 9.192s
			OK
			
	20-Cambiar la prueba y adicionar pruebas
		todo metodo que comience con test será ejecutado 
		
		class FunctionalTestCase(TestCase):
			def setUp(self):
				self.browser = webdriver.Firefox()
			def test_there_is_homepage(self):
				self.browser.get('http://localhost:8000')
				self.assertIn('Enter hash here:', self.browser.page_source)
			def test_hash_of_hello(self):
				self.browser.get('http://localhost:8000')
				text = self.browser.find_element_by_id('id_text')
				text.send_keys('hello')
				self.browser.find_element_by_name('submit').click()
				self.assertIn('2CF24DBA5FB0A30E26E83B2AC5B9E29E1B161E5C1FA7425E73043362938B9824',self.browser.page_source)
				
		Ejecutar y deberian tener un erro y un fallo
		
		Eso es TDD!!
		
			
	https://github.com/OJVELEZ/Python-TDD-Hashing.git
		
_____________________________________________________________________________________

Unit Tests

	1-Abrir archivos tests.py y comentar la clase. crear una nueva clase
	class UnitTestCase(TestCase):
		def test_home_homepage_template(self):
			response = self.client.get('/')
			print('******',response.templates[0].name)
			self.assertTemplateUsed(response,'hashing/home.html')
				
	2-Ejecutar nuevamente la prueba 
		python manage.py tests
		
		resultado:prueba fallida
		
	3-Editar settings agregar la nueva app 'hashing' en INSTALLED_APPS 
	
	4-Editar urls.py y agregar la nueva url
		from hashing import views
		urlpatterns = [
			path('admin/', admin.site.urls),
			path('', views.home, name='home'),
		]
	5-Editar views.py 		
		def home(request):
			return render(request, 'hashing/home.html')
			
	6-Crear el template	en hashing/templates/hashing/home.html
		agregar text aleatorio dentro 
	
	7-Iniciar servidor python manage.py runserver
	
	5-Correr la prueba python manage.py test
		Se ejecuta exitosamente
		response = self.client.get('/')
			la variable responsa contiene el nombre de la plantilla e incluso el código fuente
				Nombre del archivo en: response.templates[0].name
				Código fuente en: response.templates[0].source
				
		Se compara 	self.assertTemplateUsed(response,'hashing/home.html') y como se esta usando la misma plantilla

		Retorna OK

Otra prueba ahora creando un formulario y probando que exista y reciba texto
		
		1-Editar tests.py agregar un metodo dentro de la clase 	UnitTestCase
			antes importar
				from .forms import HashForm
			agrgar metodo
				def test_hash_form(self):
					form = HashForm(data={'text':'hello'})
					self.assertTrue(form.is_valid())	
		
			correr test nuevamente
				Error esperado no exite el formulario/forms
		
		2-Crear un nuevo archivo forms.py dentro de hashing
			from django import forms
			class HashForm(forms.Form):
				test = forms.CharField(label='Enter hash here:', widget=forms.Textarea)		
				
		3-Editar views.py 
		
			from django.shortcuts import render
			from .forms import HashForm

			def home(request):
				form = HashForm()
				return render(request, 'hashing/home.html', {'form': form})		
		
		4-Editar home.html , creando una forma/formulario con un campo de texto y un botón
		
			<form action="{% url 'home' %}" method="post">
			 {% csrf_token %}
			 {{ form.as_p }}

			 <input name="submit" type="submit" value="Hash" >

			</form>		
		
		5-Ejecutar nuevamente la prueba		
			Resultado exitoso
			Se prueba que:
				1-Formulario exista
				2-Que permita recibir texto
				
				
Otra prueba, probar si la libreria que importamos esta calculando correctamente el hash, usando el hash de hello
		1-Editar test
			import hashlib
			
			def test_hash_func_works(self):
				text_hash = hashlib.sha256('hello'.encode('utf-8')).hexdigest
				self.assertEqual('2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824',text_hash)
			
		
Probar BD para ver el contenido del hash alojado en la BD y  crear su propia BD temporal para tests
			
		2-Editar models.py
			class Hash(models.Model):
				text = models.TextField()
				hash = models.CharField(max_length=64)
				
				
			Ejecutar la migración (makemigrations, migrate)		

		3-Crear una nueva prueba en tests.py
		   def test_hash_object(self):
				hash = Hash()
				hash.text = 'hello'
				hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
				hash.save()

				pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
				self.assertEqual(hash.text,pulled_hash.text)
				
	
		4-Crear un BD especifica para pruebas que será temporal
			en settings.py segmento DATABASES, agregar
				'TEST_NAME': BASE_DIR / 'test.sqlite3',
				
				
		5-Ajustar la vista para almacenar la informacion
		
			def home(request):
				if request.method == 'POST':
					filled_form = HashForm(request.POST)
					if filled_form.is_valid():
						text = filled_form.cleaned_data['text']
						text_hash = hashlib.sha256(text.encode('utf-8')).hexdigest()
						try:
							Hash.objects.get(hash=text_hash)
						except Hash.DoesNotExist:
							hash = Hash()
							hash.text = text
							hash.hash = text_hash
							hash.save() 

				form = HashForm()
				return render(request, 'hashing/home.html', {'form': form})		
				
		6-Ejcutar pruebas tdas pasan  OK y ejecutar servidor y calcular hash de hello desde allí
			4 pruebas OK
			despues de la prueba del browser info queda almacenada en BD
			
			
		7-Refactor a tests.py y agregar una nueva prueba para determinar si se esta creando la página de detalle para cada hash
		
			def saveHash(self):
				hash = Hash()
				hash.text = 'hello'
				hash.hash = '2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824'
				hash.save()
				return hash

			def test_hash_object(self):
				hash = self.saveHash()
				pulled_hash = Hash.objects.get(hash='2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')
				self.assertEqual(hash.text,pulled_hash.text)

			def test_viewing_hash(self):
				hash = self.saveHash()
				response = self.client.get('/hash/2cf24dba5fb0a30e26e83b2ac5b9e29e1b161e5c1fa7425e73043362938b9824')        
				self.assertContains(response,'hello')		
		
		8-Editar urls.py agregar un nuevo path	para cumplir con la prueba anterior
			path('hash/<str:hash>', views.Hash, name='hash'),
			
		9-Editar views 
			def hash(request, hash):
			hash = Hash.objects.get(hash=hash)
			return render(request, 'hashing/hash.html', {'hash':hash})	
				
			
_____________________________________________________________________________________

Pruebas avanzadas

		1-Descomentar las pruebas funcionales
			resultado todas las pruebas OK
			
		2-En tests.py
			from django.core.exceptions import ValidationError
			
		
			
________________________________________________________________________________________

TDD 

		No es recomendable en entornos muy cambiantes
		Al iniciar el proyecto
		
		Iniciar su uso luego de la V1.0
		Priorizar funcionalidades y metodos
		Cree pruebas para lo crucial
	
	
	
		
	
		
		
	
		
		
		
		
	

	
		
		
		
		
		
	
	
	