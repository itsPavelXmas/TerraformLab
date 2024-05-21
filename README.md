# Terraform Lab

## Introducere

Terraform este un instrument de infrastructură ca si cod (IaC) dezvoltat de HashiCorp, care permite configurarea, gestionarea și versionarea infrastructurii printr-un fișier de configurare declarativ.

## Instalare Terraform

Folosim site-ul oficial al Terraform pentru a instala tool-ul. Găsiți toate detaliile necesare aici:
[![portfolio](https://img.shields.io/badge/https%3A%2F%2Fimg.shields.io%2Fbadge%2Fany_text-install-blue?style=flat&logo=terraform&label=terraform&labelColor=download&link=https%3A%2F%2Fwww.terraform.io%2Fdownloads
)](https://developer.hashicorp.com/terraform/install)


Pentru a instala Terraform pe o mașină cu un sistem de operare Ubuntu / Debian, folosim următoarele comenzi:
```
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```
Pentru a verifica dacă instalarea fost efectuată cu succes, încercăm să obținem versiunea de Terraform instalată: `terraform --version`.

Pentru a putea utiliza funcția de autocomplete cu Terraform: `terraform -install-autocomplete` .

## Terraform + Docker

În laboratorul curent o să folosim Terraform împreună cu Docker, pentru ca acest lucru să fie posibil vom avea nevoie de Docker.

### Linux
Comenzile de mai jos sunt pentru Ubuntu. Pentru alte variante de Linux (Debian, CentOS, Fedora), găsiți informații suplimentare pe pagina de documentație oficială Docker.

`sudo apt-get update `

`sudo apt-get install apt-transport-https ca-certificates curl software-properties-common`

`curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -  `

` sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" `

` sudo apt-get update ` 

` sudo apt-get install docker-ce docker-ce-cli containerd.io ` 

### Windows si MacOS

[![portfolio](https://img.shields.io/badge/https%3A%2F%2Fimg.shields.io%2Fbadge%2Fany_text-Windows-blue?style=flat&logo=docker&label=docker&labelColor=download&link=https%3A%2F%2Fdocs.docker.com%2Fdesktop%2Fget-started%2F
)](https://docs.docker.com/desktop/install/windows-install/)


[![portfolio](https://img.shields.io/badge/https%3A%2F%2Fimg.shields.io%2Fbadge%2Fany_text-MacOS-blue?style=flat&logo=docker&label=docker&labelColor=download&link=https%3A%2F%2Fdocs.docker.com%2Fdesktop%2Fget-started%2F
)](https://docs.docker.com/desktop/install/mac-install/)

Pentru a testa instalarea -> `docker container run hello-world ` 



Începem prin a crea un director nou pentru configuratia noastră:

```
mkdir lab
cd lab
```

Creăm un fișier cu numele main.tf și introducem următorul cod:
```
terraform {
 required_providers {
   docker = {
     source = "kreuzwerker/docker"
     version = "~> 2.13.0"
   }
 }
}
 
provider "docker" {}
 
resource "docker_image" "nginx" {
 name         = "nginx:latest"
 keep_locally = false
}
 
resource "docker_container" "nginx" {
 image = docker_image.nginx.latest
 name  = "tutorial"
 ports {
   internal = 80
   external = 8000
 }
}

```
Să analizăm pe rând fiecare bloc de cod introdus:

- terraform {} - conține setarile de Terraform, inclusiv provider-ul pe care urmează să îl folosim. Pentru fiecare provider, câmpul source definește numele provider-ului. By default, Terraform folosește Terraform Registry pentru a instala un provider. Astfel, în exemplul nostru, kreuzwerker/docker este un provider care se găsește în registrul Terraform la path-ul registry.terraform.io/kreuzwerker/docker. Câmpul version este opțional, dacă nu îl folosim, Terraform o să descarce by default ultima versiune disponibilă.
- provider {} - un block de tipul provider conține configurările necesare pentru ca acel provider să poată fi folosit. În cazul nostru, am folosit docker. Un provider este doar un plugin pe care Terraform îl folosește pentru a crea și pentru a gestiona resursele.
- Blocurile de tip resource - un astfel de bloc, așa cum sugerează și numele, este folosit pentru a defini resurse ale infrastructurii noastre. Așa cum putem observa, un bloc de tip resursă are 2 labels: resource "docker_image" "nginx". În acest exemplu, docker_image este tipul de resursă, iar nginx este numele resursei. Fiecare astfel de bloc are mai multe proprietăți, acestea diferă de la resursă la resursă. Exemplu: în laboratorul viitor o sa configurăm o mașină virtuală într-un provider de cloud unde o să folosim parametrii ca tipul de masină, hard disk, dimensiune hard disk, regiunea în care să fie deployed masina, etc.

Recapitulare cod:

* Am populat block-ul terraform cu config-ul unde am specificat providerul docker și versiunea dorită.
* Am inițializat providerul docker.
* Am creat o resursă de tipul docker_image cu numele nginx (numele variabilei).
* Am creat o resursă de tipul docker_container cu numele nginx. În această resursă, pentru imagine am folosit imaginea definită mai sus, iar numele container-ului este dat de câmpul ` name`.

Comenzi necesare pentru a iniția infrastructura:

* `terraform init `
Inițializează un nou director de lucru Terraform sau reinițializează unul existent.
Descarcă și instalează pluginurile necesare pentru a interacționa cu providerii specificați în fișierele de configurare (de exemplu, AWS, Azure, GCP).
Aceasta este prima comandă pe care trebuie să o rulezi într-un proiect Terraform. Fără inițializare, alte comenzi Terraform nu vor funcționa corect

* `terraform validate `
Verifică sintaxa și validitatea fișierelor de configurare Terraform.
* `terraform plan`

Generează și afișează un plan de execuție al schimbărilor necesare pentru a aduce infrastructura la starea definită în fișierele de configurare.
Nu aplică nicio schimbare, ci doar arată ce schimbări vor fi făcute.

* `terraform apply `
Aplică efectiv schimbările necesare pentru a aduce infrastructura la starea definită în fișierele de configurare.
Execută pașii specificați în planul generat de terraform plan.


## Task 1

Faceți deploy la infrastructură și observați output-ul comenzii. Pentru a observa state-ul: ` terraform show `

Verificare: 

* rulați comanda ` docker ps ` in terminal pentru a verifica daca resursa a fost creată (Faceți screenshot la output)

* rulați comanda `curl http://localhost:<port_container>` (Faceți screenshot la output)
   
## Task 2 

Modificati fișierul anterior, astfel încât container-ul să folosească portul 8080, nu `8000`. Rulați comanda pentru a vedea planul. Rulați `curl` pentru a verifica noul port.


Ștergere infrastructură - rulați comanda `terraform destroy`

## Variabile în Terraform

Avem următorul fișier `outputs.tf`, unde avem acest bloc:

```
output "Lab" {
  value="lab first output"
}
```

Pentru a observa comportamentul, urmați pașii din exercițiul anterior pentru a "aplica" infrastructura.

```
variable "LabTerraform" {
  description = "primul lab in terraform"
  default = "valoare default a primului lab in terraform"
}
```
Aplicați planul și observați comportamentul.

### Tipuri de variabile

* Lista 
```
variable "mylist" {
  default = ["ana", "are", "mere"]
}
```
Pentru a accesa un element dintr-o listă avem 2 variante.

* Folosind o funcție:

```
output "getelement" {
  value = element(var.mylist,1)
}
```
* Folosind accesare direct cu indexul:

```
output "useindex" {
  value = var.mylist[1]
}
```

## Task 3 

* Creați un fișier `variables.tf` și definiți o variabilă `docker_image` care să definească o imagine de `busybox`. 
* Creați un alt container cu imaginea definită anterior în fișierul `main.tf` existent și utilizați variabila creată.
