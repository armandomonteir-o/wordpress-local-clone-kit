# 🚀 WordPress Local Clone Kit

![Versão do Docker](https://img.shields.io/badge/docker-20.10+-blue.svg)
![Versão do WordPress](https://img.shields.io/badge/wordpress-legacy%20%26%20latest-brightgreen.svg)
![Licença](https://img.shields.io/badge/license-MIT-green.svg)

**Um boilerplate de alta performance para clonar ambientes WordPress de produção para sua máquina local usando Docker, com foco em migrações de grande volume.**

---

_[Leia em Inglês / Read in English](README.md)_

---

## O Problema

Durante minha experiência como Desenvolvedor de Software Full Stack na Conversion, precisei clonar frequentemente ambientes WordPress de clientes para minha máquina local. O que deveria ser um processo simples transformou-se em uma série de tarefas repetitivas, demoradas e sujeitas a erros. Os problemas recorrentes foram:

- **Falhas de importação:** Versões recentes do plugin All-in-One WP Migration frequentemente apresentam erros, com o upload travando em 100% ou a importação não sendo concluída.
- **Limites de upload:** Ambientes locais costumam ter limites de upload baixos por padrão, impossibilitando a importação de backups de múltiplos gigabytes, comuns em lojas e portais grandes.
- **Incompatibilidades de versão:** Pequenas diferenças nas versões do PHP ou do banco de dados entre produção e local geram problemas difíceis de depurar.
- **Funcionalidades pagas:** Ferramentas de migração moveram recursos essenciais de restauração para planos pagos, bloqueando fluxos de trabalho importantes.
- **Problemas de permissões:** A interação entre o sistema de arquivos do host e volumes Docker frequentemente causa problemas de permissão que impedem plugins de serem ativados ou funcionarem corretamente.

Após enfrentar esses problemas repetidamente, criei um template reutilizável: um ambiente limpo e pré-configurado que resolve essas questões e permite clonar sites em minutos.

## A Solução

**O WordPress Local Clone Kit** é um repositório template que fornece um fluxo de trabalho testado e repetível para clonar sites WordPress localmente. A ideia foi criar uma solução "baterias inclusas" que ataca diretamente as dores do processo, oferecendo não apenas os arquivos, mas um guia claro para transformar um processo árduo e imprevisível em um workflow rápido e confiável.

Este kit foi construído para ser a solução definitiva para os problemas que enfrentei, consolidando todas as correções e melhores práticas em um único lugar.

## Principais Funcionalidades

1. **Ambiente Docker pré-configurado:** `docker-compose.yml` (template e exemplo) orquestra WordPress, banco de dados e phpMyAdmin, pronto para ser adaptado às versões de produção.
2. **Configuração PHP para grandes importações:** `uploads.ini` aumenta limites de upload, memória e tempo de execução para permitir a importação de backups de dezenas de gigabytes.
3. **Plugin de migração funcional incluído:** Uma versão legada (6.77) do All-in-One WP Migration está incluída e pré-instalada para fornecer funcionalidades completas de importação/restore sem paywalls.
4. **Configuração de debug limpa:** `wp-config.php` personalizado que desativa a exibição de erros na tela e registra erros em `wp-content/debug.log` para depuração segura.
5. **Instruções claras de permissões:** O README contém comandos específicos para usuários Linux garantir que a propriedade dos arquivos funcione corretamente com o usuário do servidor web.

## Fluxo Essencial de Clonagem

Siga estes passos para clonar um site WordPress com este kit.

### Passo 1: Auditoria do Ambiente de Produção (Crucial)

Para criar uma cópia fiel, garanta que o ambiente local espelhe a produção.

1. Faça login no admin do WordPress do site.
2. Vá em **Ferramentas > Site Health > Informações**.
3. Anote as seguintes versões:
   - Versão do WordPress
   - Versão do PHP (Server > PHP Version)
   - Versão do servidor de banco de dados (ex.: MySQL 8.0, MariaDB 10.6)

### Passo 2: Configurar o Ambiente Local

1. Copie `docker-compose-template.yml` para `docker-compose.yml`.
2. Edite `docker-compose.yml` e substitua os placeholders pelas versões que você anotou.

### Passo 3: Subir o Ambiente e Ajustar Permissões

1. Suba os contêineres:

```bash
docker-compose up -d
```

2. Após a primeira execução, o WordPress criará arquivos na pasta `wp_files`. Ajuste as permissões conforme necessário.

#### Para usuários Linux (Ubuntu, Debian, etc.)

É crucial dar a propriedade dos arquivos ao usuário do servidor web. Este passo é obrigatório.

```bash
sudo chown -R www-data:www-data ./wp_files/
```

#### Para Windows e macOS (Docker Desktop)

Você pode pular o passo `chown`—o Docker Desktop gerencia permissões de volume. NÃO execute `chown` nessas plataformas.

### Passo 4: Instalar o WordPress e Importar

1. Acesse http://localhost:8080 e complete a instalação do WordPress (crie um usuário administrador temporário).
2. No painel, vá em **Plugins** e ative o All-in-One WP Migration incluído.
3. Use **All-in-One WP Migration > Import** para importar o backup `.wpress` do cliente.

### Passo 5: Sincronização Final

Após a importação, links internos e permalinks podem apresentar erros 404 — isso é esperado.

1. Faça login em http://localhost:8080/wp-admin (as credenciais serão as do site do cliente após a importação).
2. Vá em **Configurações > Links Permanentes** e clique em **Salvar alterações** duas vezes (não altere as configurações).

Seu site deve estar clonado e funcional.

## Tecnologias Utilizadas

O kit prioriza paridade de ambiente, robustez e portabilidade.

- **Docker**: Permite criar ambientes de desenvolvimento isolados e consistentes.
- **Imagem oficial do WordPress**: Permite combinar versões de WordPress e PHP.
- **Imagem oficial do MySQL**: Permite alinhar a versão do servidor de banco de dados com a produção.
- **phpMyAdmin**: Incluído para gerenciamento visual do banco de dados.

## Solução de Problemas

- Para resetar completamente o ambiente e recomeçar:

```bash
docker-compose down -v
sudo rm -rf ./wp_files/*
```

- Se aparecer `Container name is already in use`, rode `docker-compose down` em outro projeto ou altere os nomes dos containers no `docker-compose.yml`.

- Se a porta 3306 estiver em uso, altere a porta mapeada no `docker-compose.yml` (por exemplo `3307:3306`).

## Licença

Este projeto está licenciado sob a Licença MIT. Veja o arquivo [LICENSE](LICENSE) para mais detalhes.

## Contribuidor

Projeto idealizado e desenvolvido por Armando Monteiro.

<a href="https://github.com/armandomonteir-o">
  <img src="https://avatars.githubusercontent.com/u/141039211?v=4" width="75" height="75">
</a>
