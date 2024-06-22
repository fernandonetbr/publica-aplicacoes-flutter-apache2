Publicar uma aplicação Flutter Web em um VPS da Hostinger sem o uso de Nginx, Python e NodeJS pode ser feito utilizando apenas o servidor web Apache, que é comumente suportado por VPSs. Vou guiá-lo através de um processo simples utilizando Apache.

**Passo 1: Preparar o Ambiente no VPS**

Acessar o VPS via SSH:
Conecte-se ao seu VPS usando um cliente SSH como o PuTTY ou o terminal.

ssh usuario@seu-ip-vps
Instalar o Apache:
Se o Apache não estiver instalado, você pode instalá-lo com o seguinte comando:

sudo apt update
sudo apt install apache2 -y

**Passo 2: Build da Aplicação Flutter Web**

Build da Aplicação:
No seu ambiente de desenvolvimento local, dentro do diretório do seu projeto Flutter, execute o comando para gerar os arquivos da build web.

flutter build web
Isso criará uma pasta build/web contendo os arquivos da sua aplicação web.

**Passo 3: Transferir os Arquivos para o VPS**

Transferir os Arquivos:
Utilize SCP (Secure Copy Protocol) para transferir os arquivos da build para o VPS. No seu terminal local, execute:

scp -r build/web/* usuario@seu-ip-vps:/caminho/para/diretorio/publico
Altere /caminho/para/diretorio/publico para o diretório onde você deseja colocar os arquivos no servidor VPS. Normalmente, no Apache, o diretório padrão é /var/www/html.

**Passo 4: Configurar o Apache**

Configurar o Apache:
Se necessário, edite o arquivo de configuração do Apache para assegurar que ele aponte para o diretório correto onde você colocou os arquivos da build Flutter.

Abra o arquivo de configuração do Apache:

sudo nano /etc/apache2/sites-available/000-default.conf
Modificar o Diretório Root:
Modifique a linha DocumentRoot para apontar para o diretório onde você colocou os arquivos da build Flutter.

DocumentRoot /caminho/para/diretorio/publico
Salvar e Sair:
Salve as mudanças e saia do editor (no nano, você pode fazer isso pressionando Ctrl+X, depois Y e Enter).

Reiniciar o Apache:
Após fazer as alterações, reinicie o Apache para aplicar as mudanças.

sudo systemctl restart apache2

**Passo 5: Acessar sua Aplicação**
Agora, você deve ser capaz de acessar sua aplicação Flutter Web através do endereço IP do seu VPS ou do domínio configurado.

Por exemplo:

http://seu-ip-vps

Copiar
ou

http://seu-dominio

Copiar

**Conclusão**

Seguindo esses passos, você pode publicar sua aplicação Flutter Web em um VPS com o Apache sem a necessidade de utilizar Nginx, Python ou NodeJS. Certifique-se de que todos os arquivos foram corretamente transferidos e que o Apache está configurado para apontar para o diretório correto onde os arquivos da build foram colocados.

Para garantir que sua aplicação Flutter Web esteja segura e utilize HTTPS, você pode configurar o Apache com um certificado SSL. Uma maneira comum e gratuita de conseguir isso é utilizando o Let's Encrypt, que oferece certificados SSL gratuitos. Aqui está um guia passo a passo para configurar HTTPS no Apache usando Let's Encrypt.

### Passo 1: Instalar o Certbot

1. **Adicionar o Repositório Certbot:**
   Primeiro, adicione o repositório do Certbot para que possamos instalar a versão mais recente.

   ```sh
   sudo apt update
   sudo apt install software-properties-common
   sudo add-apt-repository universe
   sudo add-apt-repository ppa:certbot/certbot
   sudo apt update
   ```

2. **Instalar o Certbot:**
   Instale o Certbot e o plugin do Apache.

   ```sh
   sudo apt install certbot python3-certbot-apache
   ```

### Passo 2: Obter o Certificado SSL

1. **Executar o Certbot:**
   Execute o Certbot para obter e instalar o certificado SSL para seu domínio. Substitua `seu-dominio` pelo seu domínio real.

   ```sh
   sudo certbot --apache -d seu-dominio
   ```

2. **Siga as Instruções:**
   O Certbot irá guiá-lo através do processo de configuração. Você precisará fornecer um e-mail para notificações de renovação e aceitar os Termos de Serviço.

### Passo 3: Configurar o Apache para Forçar HTTPS

1. **Forçar Redirecionamento para HTTPS:**
   Após instalar o certificado, você pode configurar o Apache para redirecionar automaticamente todo o tráfego HTTP para HTTPS.

   Abra o arquivo de configuração do site:

   ```sh
   sudo nano /etc/apache2/sites-available/000-default-le-ssl.conf
   ```

   Adicione a seguinte linha dentro do bloco `<VirtualHost *:80>`:

   ```apache
   <VirtualHost *:80>
       ServerName seu-dominio
       Redirect permanent / https://seu-dominio/
   </VirtualHost>
   ```

2. **Habilitar o Módulo de Redirecionamento:**
   Certifique-se de que o módulo de redirecionamento está habilitado.

   ```sh
   sudo a2enmod rewrite
   ```

3. **Reiniciar o Apache:**
   Reinicie o Apache para aplicar as mudanças.

   ```sh
   sudo systemctl restart apache2
   ```

### Passo 4: Testar a Configuração

1. **Verificar a Configuração:**
   Acesse seu domínio em um navegador para verificar se o redirecionamento para HTTPS está funcionando corretamente.

   ```sh
   http://seu-dominio
   https://seu-dominio
   ```

   Ambas as URLs devem redirecionar para a versão HTTPS do seu site.

### Passo 5: Configurar a Renovação Automática do Certificado

1. **Configurar Renovação Automática:**
   O Certbot configura automaticamente a renovação automática para você. No entanto, é uma boa prática verificar se o processo está configurado corretamente. Execute o seguinte comando para testar a renovação:

   ```sh
   sudo certbot renew --dry-run
   ```

### Conclusão

Seguindo esses passos, você configurará o Apache para usar HTTPS com um certificado SSL gratuito fornecido pelo Let's Encrypt. Isso garante que a comunicação entre os usuários e seu servidor seja criptografada e segura. Certifique-se de que o redirecionamento de HTTP para HTTPS está funcionando corretamente e que a renovação automática do certificado está configurada para evitar interrupções no serviço.
