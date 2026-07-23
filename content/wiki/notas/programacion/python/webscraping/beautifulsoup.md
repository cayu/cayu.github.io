---
title: "Beautiful Soup"
date: 2019-08-21T14:50:00-03:00
---

Beautiful Soup es una biblioteca de Python para analizar documentos HTML. Esta biblioteca crea un árbol con todos los elementos del documento y puede ser utilizado para extraer información

```python
# -*- coding: utf-8 -*-
from bs4 import BeautifulSoup as bs #A partir de ahora nos referimos a BeautifulSoup como 'bs'
import requests
url = "https://www.xataka.com/record/20"
cont = 0 #Contador, lo usaremos mas adelante
#Realizamos la peticion a la pagina
req = requests.get(url)

if req.status_code== 200:
	html = bs(req.text, "html.parser") #Pasamos el contenido HTML a un objeto BeautifulSoup()
	# Obtenemos todos los articulos del primer tipo
	entradas = html.find_all('article', {'class': 'recent-abstract abstract-article'})
	# Lo mismo con el segundo tipo de articulos (los de la imagen mas grande)
	entradas2 = html.find_all('article', {'class': 'recent-abstract abstract-article m-featured'})
	#Agregamos cada item de la segunda lista de articulos a la primer lista
	for item in entradas2:
		entradas.append(item)

# Recorremos todas las entradas para extraer el titulo y link
for item in entradas:
	cont += 1 #Contador autoincremental para llevar registro de la cantidad de articulos
	titulo = item.find('h2', {'class': 'abstract-title'}).getText()
	link = item.find('a').get('href')
	# Imprimo el Titulo y link de las entradas
	print("Id........: %d" % cont)
	print("Titulo....: " + titulo)
	print("Link:.....: " + link)
	print("___________________________")
```

Ref.: UnderDocs \#01
