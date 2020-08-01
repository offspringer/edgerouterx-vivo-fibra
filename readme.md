# Configuração de EdgeRouter-X para Vivo Fibra

## Introdução
Esta configuração foi criada com o intuito de substituir completamente o hardware disponibilizado pela Vivo Fibra e utilizar o máximo de recursos possíveis do roteador EdgeRouter-X, mantendo os recursos de Internet e TV (no caso, IPTV).

## Considerações
* **Minimalismo**: manter a menor quantidade de equipamentos possíveis, com as menores dimensões possíveis, podendo instalá-los em caixas de distribuição de sistemas de apartamentos, por exemplo;
* **Qualidade**: obter um ganho de qualidade ao substituir o equipamento original por um mais confiável.

## Estrutura da rede
```
             fibra
               |
       +---------------+
       |      ONT      |
       +---------------+
               |
       vlan10 (internet)
         vlan20 (iptv)
         vlan30 (voip)
               |
       +-------+     
       |
       | eth0
   +-----------------------+
   |      EdgeRouter-X     |
   +-----------------------+
   eth1~3 |            | eth4
          |            |  
          |            +----------------------+     
          |                                   |    
   +------------------------+         +-----------------------+   
   | Wifi AP / Switch / PC  |         |   Decodificador IPTV  |    
   +------------------------+         +-----------------------+   
```

## Hardware utilizado
* [EdgeRouter-X](https://www.ui.com/edgemax/edgerouter-x/) - modelo: ER-X, firmware: v2.0.8-hotfix.1

## Pré-requisitos
1. ONT corretamente configurada e autenticada na Vivo Fibra
2. ONT conectada na porta eth0 (WAN) do EdgeRouter-X
3. Access Points, switches ou computadores conectados nas portas eth1, eth2 e eth3;
4. Decodificador IPTv conectado na porta eth4;

## Utilização
1. Copie o arquivo `config` para dentro de seu **EdgeRouter-X** através de `ssh`
2. Import as configurações com o comando `source config`
3. Finalize com `commit` e `save`
4. Reinicie o roteador

Pronto, já pode testar sua conexão com a Internet e o funcionamento da IPTV.

## Problemas conhecidos
### ONT
Algumas ONTs podem não ser compatíveis com a sua região (uma rápida pesquisa no Google pode ajudar);

### Rede
A rede pode ficar lenta se o multicast não estiver funcionando da maneira mais otimizada possível. Se estiver utilizando um switch para distribuir a IPTV para muitos pontos, verifique se o mesmo é compatível com *IGMP Snooping* e se o recurso está habilitado.

### IPTV
Como a IPTV depende de muitos fatores para funcionar, diversos problemas podem acontecer nesta parte: o decodificador pode não finalizar o setup inicial, a tela pode ficar preta com mensagem de "conteúdo indisponível" após algum tempo, etc.

As causas mais comuns seriam:
1. Rotas estáticas: deve-se verificar se algumas rotas estáticas fornecidades pela Vivo Fibra foram alteradas. Infelizmente, não há muito o que fazer nesta parte, a não ser comparar com as que o roteador original recebe através do protocolo de atualização TR-069;
2. Servidores DNS: assim como as rotas estáticas, eles também podem mudar com o tempo e impedir o correto funcionamento da IPTV;
3. IGMP Proxy: este componente é essencial para o correto funcionamento da IPTV, e pode apresentar uma certa instabilidade dependendo da versão do firmware do roteador. Portanto, é sempre válido verificar se o mesmo ainda está rodando através do comando `ps aux | grep igmp` e também analisar como está o multicast através dos comandos `show ip multicast interfaces` e `show ip multicast mfc`.

### VoIP
A configuração de VoIP não foi testada pois não possuo este serviço em minha assinatura;

## Referências
* Documentação do Vyatta: https://docs.huihoo.com/vyatta/6.3/
* Substituindo roteador Vivo Fibra por GPON TP-LINK TX-6610 + Mikrotik: https://forum.vivo.com.br/threads/112037-Substituindo-roteador-Vivo-Fibra-por-GPON-TP-LINK-TX-6610-Mikrotik
* Como configurar um serviço SIP para utilização de VoIP: https://forum.vivo.com.br/threads/114368-Configura%C3%A7%C3%A3o-SIP