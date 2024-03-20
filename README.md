## Atividade 01 - Março de 2024 - Configuração de Rede em Docker

Você foi designado para configurar um ambiente de rede em Docker para uma empresa fictícia. Esse ambiente deve abranger serviços essenciais de rede, como DHCP, DNS e Firewall, para garantir conectividade e segurança adequadas. Cada serviço deve ser configurado em um container Docker separado, e é necessário garantir sua comunicação adequada entre si. Além disso, deve-se criar Dockerfiles para cada imagem necessária, baseados na imagem ubuntu:latest, e realizar testes para validar a configuração da rede.

## Estrutura do Projeto

- dockerfile.dhcp
- dockerfile.dns
- dockerfile.firewall
- dhcpd.conf (arquivo de configuração necessário para DHCP)
- firewall_rules.sh (arquivo de configuração necessário para FIREWALL)
- named.conf.options (arquivo de configuração necessário para DNS)
- README.md

## Pré-requisitos

- Sistema de criação (HOST): Linux Mint 21.3
- Docker 24.0.5

## Instruções de Uso

1. Clone o repositório do Github.
2. Acesse-o pelo terminal na pasta onde o projeto foi clonado.
3. Execute o comando "sudo docker build -t nikolas.vale/dhcp:latest -f dockerfile.dhcp ." para iniciar a criação do container DHCP.
4. Execute o comando "sudo docker build -t nikolas.vale/dns:latest -f dockerfile.dns ." para iniciar a criação do container DNS.
5. Execute o comando "sudo docker build -t nikolas.vale/firewall:latest -f dockerfile.firewall ." para iniciar a criação do container FIREWALL.
6. Após a criação do container DHCP, digite "sudo docker run -d --net rede --name dhcp nikolas.vale/dhcp" para iniciar a execução do container DHCP.
7. Após a criação do container DNS, digite "sudo docker run -d --net rede --name dns nikolas.vale/dns" para iniciar a execução do container DNS.
8. Após a criação do container FIREWALL, digite "sudo docker run -d --name firewall --restart always --net rede --privileged nikolas.vale/firewall" para iniciar a execução do container FIREWALL.
9. Se necessário adentrar no container em execução, use "sudo docker exec -it (dhcp/dns/firewall) /bin/bash", substituindo (dhcp/dns/firewall) pelo nome atribuído a cada um dos containers.
10. Se necessário parar a execução do container, use "sudo docker stop (dhcp/dns/firewall)", substituindo (dhcp/dns/firewall) pelo nome atribuído a cada um dos containers.

## Rede

- A rede foi configurada com o nome "rede" e o endereço "192.168.1.0/24", utilizando o comando "sudo docker network create --subnet=192.168.1.0/24 rede".
- Para conectar os containers, utilize "--net rede" em cada um, permitindo a comunicação entre eles na mesma rede, conforme mostrado nas instruções de uso.

## Funcionamento

- Container DHCP: Configurado com o arquivo "dhcpd.conf", contendo as configurações necessárias para o serviço DHCP. A porta 67/UDP foi aberta no container para permitir que o servidor DHCP atribua endereços IP automaticamente aos dispositivos conectados à rede através dessa porta. Foi reservada a faixa de endereços de 192.168.1.100 a 192.168.1.200 para atribuição aos demais containers.

- Container DNS: Configurado com o arquivo "named.conf.options", contendo as configurações necessárias para o serviço DNS. A porta 53 TCP/UDP foi aberta no container para permitir a resolução de nomes na rede.

- Container FIREWALL: Configurado com o arquivo "firewall_rules.sh", contendo as configurações necessárias para o serviço FIREWALL. Seu funcionamento envolve bloquear o acesso a todas as portas, exceto às portas DHCP e DNS liberadas nos containers anteriores.

## Testes

- DHCP:
  - Para testar o funcionamento do servidor DHCP, conecte um novo dispositivo à rede e verifique se ele recebe um endereço IP automaticamente através do comando "ip a" no terminal do dispositivo.
  - Ou, adentre o container DHCP e verifique os endereços atribuídos usando o comando "tail -f /var/log/dhcpd.log".
  - Uma maneira eficaz de teste é verificar se os endereços IP dos outros containers foram atribuídos corretamente dentro da faixa de IP especificada (192.168.1.100 a 192.168.1.200), adentrando nos containers "dns" ou "firewall".

- DNS:
  - Para testar o funcionamento do servidor DNS, tente acessar um site pelo nome em vez do endereço IP usando o comando "ping" no terminal do dispositivo.
  - Ou, adentre o container DNS e verifique a resolução dos endereços através do comando "tail -f /var/log/named/named.log".
  - Ou, adentre o container DNS e verifique a resolução dos nomes usando o comando "dig www.example.com" ou outro site específico para obter o IP correspondente.

- FIREWALL:
  - Para testar o funcionamento do FIREWALL, tente acessar um site pelo nome em vez do endereço IP usando o comando "ping" no terminal do dispositivo.
  - Ou, adentre o container FIREWALL e verifique se as portas estão bloqueadas usando o comando "iptables -L", onde apenas as portas liberadas (53 e 67:68) devem ser exibidas.