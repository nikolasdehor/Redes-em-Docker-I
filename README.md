## Atividade 01 - Março de 2024 - Exercício de Configuração de Rede no Docker

Você foi designado para configurar um ambiente de rede no Docker para uma empresa fictícia. Este ambiente deve incluir serviços essenciais de rede, como DHCP, DNS e Firewall, para assegurar uma conectividade e segurança adequadas. Você deverá configurar cada serviço em um container Docker separado e garantir a comunicação eficaz entre eles. Além disso, é necessário criar Dockerfiles para cada uma das imagens necessárias, usando como base a imagem `ubuntu:latest`, e realizar testes para validar a configuração da rede.

## Estrutura do Projeto

- `dockerfile.dhcp`
- `dockerfile.dns`
- `dockerfile.firewall`
- `dhcpd.conf` (arquivo de configuração necessário para o DHCP)
- `firewall_rules.sh` (arquivo de configuração necessário para o Firewall)
- `named.conf.options` (arquivo de configuração necessário para o DNS)
- `README.md`

## Pré-requisitos

- Sistema operacional host: Linux Mint 21.3
- Docker versão 24.0.5

## Instruções de Uso

1. Clone o repositório do GitHub.
2. Utilize o terminal para acessar a pasta onde o projeto foi clonado.
3. Execute o comando `sudo docker build -t nikolas.vale/dhcp:latest -f dockerfile.dhcp .` para iniciar a criação do container DHCP.
4. Execute o comando `sudo docker build -t nikolas.vale/dns:latest -f dockerfile.dns .` para iniciar a criação do container DNS.
5. Execute o comando `sudo docker build -t nikolas.vale/firewall:latest -f dockerfile.firewall .` para iniciar a criação do container Firewall.
6. Após a criação do container DHCP, digite `sudo docker run -u root -d --name dhcp nikolas.vale/dhcp` para iniciar a execução do container DHCP.
7. Após a criação do container DNS, digite `sudo docker run -u root -d --name dns nikolas.vale/dns` para iniciar a execução do container DNS.
8. Após a criação do container Firewall, digite `sudo docker run -u root -d --name firewall nikolas.vale/firewall` para iniciar a execução do container Firewall.
9. Se necessário, para acessar um container em execução, utilize `sudo docker exec -it <nome_do_container> /bin/bash`, substituindo `<nome_do_container>` por `dhcp`, `dns` ou `firewall`.
10. Para parar a execução de um container, utilize `sudo docker stop <nome_do_container>`, substituindo `<nome_do_container>` conforme necessário.

## Funcionamento

- **Container DHCP:** Este container é configurado com o arquivo `dhcp.conf`, contendo as configurações necessárias para o serviço DHCP. A porta 67/UDP é aberta, permitindo que o servidor DHCP atribua endereços IP a novos dispositivos conectados à rede.

- **Container DNS:** Configurado com o arquivo `named.conf.options`, este container contém as configurações necessárias para o serviço DNS. As portas 53 TCP/UDP são abertas, possibilitando a resolução de nomes na rede.

- **Container Firewall:** Este container utiliza o arquivo `firewall_rules.sh` para definir as regras do Firewall, bloqueando o acesso a todas as portas exceto aquelas especificadas para os serviços DHCP e DNS.

## Testes

- **DHCP:**
  - Conecte um novo dispositivo à rede e verifique se ele recebe um endereço IP automaticamente utilizando o comando `ip a` no terminal do dispositivo.
  - Ou, acesse o container DHCP e verifique os endereços atribuídos com `tail -f /var/log/dhcpd.log`.

- **DNS:**
  - Teste a resolução de nomes tentando acessar um site pelo seu nome, em vez de seu endereço IP, com o comando `ping` no terminal do dispositivo.
  - Ou, acesse o container DNS e verifique as resoluções de nomes com `tail -f /var/log/named/named.log`.
  - Alternativamente, verifique a resolução de um site específico com `dig www.example.com`.

- **Firewall:**
  - Tente acessar um site pelo nome para testar a eficácia do Firewall.
  - Ou, acesse o container Firewall e verifique as portas bloqueadas e liberadas com `iptables -L`.
```