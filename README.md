# Escola no Prazo - Network Architect Solutions

Rede do Grupo 6 para o projeto CitizenTech Escola no Prazo.

**Repositório:** https://github.com/GS-2026-2/Network

## Entrega

- `Escola-no-Prazo-G6.pkt`: simulação da rede e página HTTP com a ficha do projeto.
- `Relatorio-Network-G6.pdf`: ficha, topologia, plano de IPs, roteiro e evidências dos testes.
- `evidencias/`: capturas reais do Packet Tracer.
- `configuracoes/`: configurações salvas dos roteadores e do switch.

A entrega acadêmica prevista é o `.pkt` e o PDF. As outras pastas permitem reproduzir e conferir o trabalho.

## Integrantes

- Gabriel Molinari Droppa - RM 562082
- Isabela de Deus - RM 565988
- João Pedro Godinho Passiani - RM 561602
- Thomaz Vasconcelos Mendes - RM 564805
- Vítor Silva Borsato - RM 561805

## Objetivo

Apoiar a organização das demandas escolares, com responsáveis, prazos, dependências e confirmação de recebimento. O piloto acompanha a preparação de uma apresentação de boas práticas. A referência é o CEMEI Alípio Pereira dos Santos, Jaguaribe, Osasco - SP.

O laboratório demonstra a infraestrutura da solução: equipe, Wi-Fi público e servidores separados, com acesso controlado e saída para uma Internet simulada.

## Abrir

1. Use Cisco Packet Tracer 9.0 (versão usada na validação: 9.0.0.0810).
2. Abra `Escola-no-Prazo-G6.pkt` e aguarde os enlaces ficarem verdes.
3. Espere o DHCP e a convergência inicial antes dos testes. Se o primeiro ping expirar, repita.

## Plano de rede

| Segmento | Rede | Gateway / endereços |
|---|---|---|
| VLAN 10 - EQUIPE | 192.168.6.0/26 | Gateway 192.168.6.1; PC por DHCP |
| VLAN 20 - CIDADAO | 192.168.6.64/26 | Gateway 192.168.6.65; visitante por DHCP |
| VLAN 30 - SERVIDORES | 192.168.6.128/26 | Gateway 192.168.6.129; DNS/HTTP 192.168.6.130 |
| Escola - provedor | 203.0.113.0/30 | Escola .2; provedor .1 |
| Internet simulada | 198.51.100.0/24 | Gateway .1; servidor externo .10 |

A máscara das VLANs é 255.255.255.192. O trunk usa 802.1Q e router-on-a-stick. O roteador distribui DHCP por VLAN e faz PAT no endereço WAN. A ACL permite DNS/HTTP ao servidor interno a partir do Wi-Fi público, bloqueia o restante da LAN e permite acesso externo.

## Testes

1. **DHCP:** PC-COORDENACAO > Desktop > IP Configuration > DHCP. No teste recebeu 192.168.6.2, gateway .1 e DNS .130. O visitante recebeu .66, gateway .65 e DNS .130.
2. **DNS/HTTP:** VISITANTE > Desktop > Web Browser > `http://escola.test`. A ficha deve aparecer, com o link do repositório.
3. **Isolamento:** VISITANTE > Desktop > Command Prompt > `ping 192.168.6.2`. Deve ser bloqueado pela ACL. Confira o IP atual do PC se o DHCP tiver distribuído outro endereço.
4. **Entre VLANs:** no PC-COORDENACAO, execute `ping 192.168.6.130`. Deve responder; na validação foram 4/4 respostas.
5. **Internet:** no PC-COORDENACAO, execute `ping 198.51.100.10`. Após convergir, foram 4/4 respostas.
6. **SSH:** no PC-COORDENACAO, execute `ssh -l admin 192.168.6.1`, autentique e confira o prompt `R-ESCOLA#`.
7. **NAT:** logo depois do ping externo, na sessão SSH execute `show ip nat translations`. Foram registradas traduções ICMP de 192.168.6.2 para 203.0.113.2. Se a tabela estiver vazia, repita o ping, pois as entradas expiram.
8. **Configurações:** `show running-config` nos roteadores e switch; `show access-lists` no roteador. Use Espaço para avançar a saída paginada.

## Credenciais fictícias do laboratório

- R-ESCOLA: usuário `admin`, senha `Escola6Admin`.
- Enable secret: `Prazo6-Priv`.
- Wi-Fi: SSID `Escola-Visitantes`, WPA2-PSK `Escola6-Wifi`.

Essas credenciais pertencem somente à simulação acadêmica. O acesso remoto do R-ESCOLA aceita apenas SSH.

## Validação

DHCP, DNS/HTTP e bloqueio pela ACL estão documentados. Em 10/11/2026 foram confirmados SSH, tabela NAT, ping entre VLANs e repetição do ping externo sem perda. As capturas e os trechos reais de `show running-config` constam no PDF e em `evidencias/`.

A Internet deste arquivo é simulada pelo provedor e pelo servidor externo. O endereço GitHub na ficha é uma referência para abrir no navegador real; não há uma conexão do Packet Tracer à Internet pública.
