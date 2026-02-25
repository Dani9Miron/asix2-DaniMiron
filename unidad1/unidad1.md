---
layout: default
title: "Sprint 1"
---

## Script Linux

Creem fitxer per a ficar el script
<img width="491" height="27" alt="image" src="https://github.com/user-attachments/assets/ba95cccb-34a7-409d-9a1d-ded04cf60cda" />

Afegim el script i guardem 
<img width="598" height="338" alt="image" src="https://github.com/user-attachments/assets/dbc4d9f1-f274-4064-8d81-afe154c19071" />

Donem permisos al archiu
<img width="534" height="28" alt="image" src="https://github.com/user-attachments/assets/c66e621f-9207-48f8-9fd6-252fd300db9b" />

Entrem al sevice que hem creat
<img width="600" height="23" alt="image" src="https://github.com/user-attachments/assets/de6d3b4d-45de-48a9-8f9f-30ae1826980c" />

Afegim al service el que ens fa fallta
<img width="599" height="166" alt="image" src="https://github.com/user-attachments/assets/7a8e210b-da7c-4653-a2a8-e3f15d7efd3f" />

Donem permisos
<img width="597" height="46" alt="image" src="https://github.com/user-attachments/assets/bbfb5d17-6e4c-43c1-aa3c-695080a98c66" />

Creem el target i entrem dins 
<img width="597" height="44" alt="image" src="https://github.com/user-attachments/assets/9f15011c-05a4-4434-96e7-762a7dc734b0" />

Fiquem un unit i install i afegim lo que que fica 
<img width="599" height="220" alt="image" src="https://github.com/user-attachments/assets/ed6ab9aa-5110-462a-b743-bb1d3ab114af" />


Donem permisos i daemon-reexec: reinicia el procés systemd (sense reiniciar el sistema).
daemon-reload: recarrega la configuració dels serveis (després de modificar fitxers 
<img width="594" height="91" alt="image" src="https://github.com/user-attachments/assets/c4f75396-4afb-4864-a6c6-44a4100d3fce" />


Iniciem  servei 
<img width="589" height="67" alt="image" src="https://github.com/user-attachments/assets/099692bc-5dfd-40f1-9e96-3c70ac69d12c" />

I aqui veiem com ens a arribat el correu

<img width="557" height="210" alt="image" src="https://github.com/user-attachments/assets/37d39d8c-6bfa-4444-a92b-047995e1a9e5" />

# Script Windows

Creem les Carpetes 
<img width="634" height="426" alt="image" src="https://github.com/user-attachments/assets/a9f5c6d2-db8c-4b3f-9734-9de92df59c1f" />
<img width="795" height="198" alt="image" src="https://github.com/user-attachments/assets/0a924f6f-3b68-4c3d-a397-616a2d530b65" />

Creem el script 
<img width="918" height="901" alt="image" src="https://github.com/user-attachments/assets/a200707a-e350-48ae-8dc6-2ba12b0735b1" />

Creem el servei

<img width="432" height="228" alt="image" src="https://github.com/user-attachments/assets/2cdb0b6d-cc42-4ea2-9f88-e4e9d01fff8b" />
<img width="355" height="152" alt="image" src="https://github.com/user-attachments/assets/59c54db8-9f3c-444a-aed9-b6476356a696" />

Aqui veem com esta el servei
<img width="916" height="586" alt="image" src="https://github.com/user-attachments/assets/45278954-bc95-4b55-a4ed-b29a500636b8" />

Reiniciem el sistema i veurem com se obri el nostre script
<img width="899" height="890" alt="image" src="https://github.com/user-attachments/assets/23f7eef9-4f72-446f-a8b9-85bf9e41734d" />



# Configuració del Certificat SSL (Windows Server AD CS y IIS)

Aquest document detalla el procés complet de depuració i configuració per a habilitar HTTPS en el servidor IIS utilitzant una Entitat de Certificació (AD CS) pròpia.

## 1. Problema Inicial: Error al generar certificat
Es va intentar generar el certificat mitjançant PowerShell (`Get-Certificate`), però va fallar per incompatibilitat de noms de plantilla o permisos. La consola gràfica `certlm.msc` mostrava que la plantilla "Web Server-SAN" no estava disponible.

![Error plantilla no disponible](img/02_error_plantilla.png)
*Error: "No tiene permiso para solicitar este tipo de certificado"*

## 2. Solució de Permisos (Windows)
Per a permetre que la màquina (i no l'usuari) sol·liciti el certificat, es van haver de modificar els permisos de la plantilla a la consola `certtmpl.msc`:
1.  Es va afegir el grup **Controladores de dominio** (ja que el servidor és un DC) i **Equipos del dominio**.
2.  Es va concedir el permís de **Inscribirse** (Enroll).

![Permisos de la plantilla](img/03_permisos_plantilla.png)

## 3. Problema de Confiança (Untrusted Root)
En intentar inscriure el certificat després d'arreglar els permisos, va aparèixer l'error `0x800b0109 (CERT_E_UNTRUSTEDROOT)`. Això passa perquè el servidor no confiava en la seva pròpia CA autofirmada recentment creada.

![Error Untrusted Root](img/04_error_untrusted.png)

**Solució:**
1.  Exportar el certificat arrel de la CA (`root.cer`).
2.  Instal·lar-lo manualment en el magatzem **"Entidades de certificación raíz de confianza"** de l'equip local.

## 4. Generació Correcta del Certificat
Un cop solucionada la confiança, es va poder sol·licitar el certificat via `certlm.msc` configurant:
*   **Nom Comú (CN):** `dani.local`
*   **Nom Alternatiu (SAN - DNS):** `dani.local`

![Configuració del certificat](img/05_config_cert_san.png)

## 5. Configuració del Servidor Web (IIS)
Amb el certificat generat, es va assignar al lloc web en l'IIS Administrator:
*   Enllaç (Binding): HTTPS port 443.
*   Certificat SSL: Seleccionar `dani.local` (el nou, no el de la CA).

![Binding en IIS](img/06_iis_binding.png)

## 6. Configuració de DNS (Hosts)
Com que `dani.local` no existeix a Internet, es va modificar l'arxiu `hosts` per a apuntar el domini a la IP local (`127.0.0.1` en Windows, IP real en clients).

**Comanda PowerShell utilitzada:**
```powershell
Add-Content -Path "C:\Windows\System32\drivers\etc\hosts" -Value "127.0.0.1 dani.local"
```

![Fix Hosts en PowerShell](img/07_dns_hosts.png)

---

## 7. Configuració del Client (Ubuntu)
Per a validar el funcionament des d'una altra màquina:

1.  **Configuració DNS:** Es va afegir la IP del servidor Windows (`192.168.203.150`) a `/etc/hosts`.
    ![Ping correcte des d'Ubuntu](img/08_ubuntu_ping.png)

2.  **Instal·lació del Certificat Arrel:**
    Es va copiar el fitxer `root.cer` a Ubuntu i es va instal·lar per a evitar l'error de "Lloc no segur".
    ```bash
    sudo cp root.cer /usr/local/share/ca-certificates/dani-ca.crt
    sudo update-ca-certificates
    ```

    ![Instal·lació CA en Ubuntu](img/09_ubuntu_cert.png)

**Resultat final:** Accés segur HTTPS des del client sense advertències de seguretat.

## Configuració de Servidor LAMP amb SSL Auto signat


Instal·larem el "cor" del servidor web.

<img width="568" height="51" alt="image" src="https://github.com/user-attachments/assets/ff92633f-0697-42ab-88e9-3cc9c5ef8faf" />

<img width="600" height="260" alt="image" src="https://github.com/user-attachments/assets/ff60fec6-dc3c-462c-8123-0a5b6c485f78" />


Apache2: És el servidor web que rep les peticions HTTP/HTTPS i serveix les pàgines.
MariaDB: El gestor de bases de dades (fork de MySQL).
PHP: El llenguatge de programació que processarà el codi del costat del servidor.
libapache2-mod-php: El "pont" que permet que Apache entengui i executi codi PHP.


Anem a crear la prova de concepte perquè el servidor ens saludi.

<img width="553" height="134" alt="image" src="https://github.com/user-attachments/assets/7bb0b9d4-fb9a-4dbe-ac40-ee53aac1a239" />

/var/www/html és l'arrel pública del servidor. Qualsevol fitxer aquí és accessible des del navegador. La funció phpinfo() és vital en sistemes operatius per verificar que tots els mòduls de PHP s'han carregat correctament.



Perquè una pàgina funcioni sota HTTPS, necessita un certificat que xifri la comunicació entre el client i el servidor.

<img width="601" height="357" alt="image" src="https://github.com/user-attachments/assets/fb79009b-cc2c-4350-9aa6-5d7931e534dc" />

req -x509: Especifica que volem un certificat auto-signat.
rsa:2048: Crea una clau RSA de 2048 bits (estàndard de seguretat).
keyout: On es guarda la clau privada (prohibit compartir-la!).
out: On es guarda el certificat públic.


 Configuració del Virtual Host SSL
Ara hem de dir-li a Apache que utilitzi aquests fitxers per al port 443 (HTTPS).
Editem el fitxer de configuració:

<img width="588" height="519" alt="image" src="https://github.com/user-attachments/assets/63dbc649-6830-4336-930f-e96a95c6ab4b" />

Hem de modificar o verificar aquestes línies:
DocumentRoot /var/www/html (Asegura't que apunta on tens l'index.php).
SSLCertificateFile /etc/ssl/certs/servidor.crt
SSLCertificateKeyFile /etc/ssl/private/servidor.key


Activació de mòduls i reinici
Apache no activa el xifrat per defecte per estalviar recursos. L'hem d'activar manualment.

<img width="600" height="140" alt="image" src="https://github.com/user-attachments/assets/b4091e31-33f5-4ddd-a280-745f866decfd" />

<img width="545" height="88" alt="image" src="https://github.com/user-attachments/assets/922e2e9e-2e57-413d-b81c-3ad0046fd8e6" />


Fiquem el index.php davant de tot per a que funcione
<img width="535" height="86" alt="image" src="https://github.com/user-attachments/assets/6f3b5608-03df-4eca-b44c-608cff8712b3" />

<img width="601" height="541" alt="image" src="https://github.com/user-attachments/assets/ca804ccf-2c6c-4cf4-9866-744ad4f58630" />















