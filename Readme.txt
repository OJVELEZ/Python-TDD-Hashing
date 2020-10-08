
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
		
	
		
		
		
	

	
		
		
		
		
		
	
	
	