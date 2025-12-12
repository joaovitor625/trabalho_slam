# Trabalho SLAM - Robótica Avançada

Sistema de localização e mapeamento simultâneo (SLAM) desenvolvido como trabalho acadêmico para a disciplina de Robótica Avançada da UNIFEI (8º Período).

## 📋 Sobre o Projeto

Este projeto implementa um sistema completo de SLAM (Simultaneous Localization and Mapping) utilizando o framework ROS (Robot Operating System). O sistema integra:

- **Mapeamento do ambiente**: Criação de mapas do espaço de trabalho
- **Localização**: Determinação precisa da posição do robô usando AMCL (Adaptive Monte Carlo Localization)
- **Navegação remota**: Controle do robô para atingir objetivos específicos
- **Visualização**: Interface RVIZ para monitoramento em tempo real

## 🏗️ Estrutura do Projeto

```
trabalho_slam/
├── config/                          # Arquivos de configuração
│   ├── amcl_params.yaml            # Parâmetros do AMCL
│   ├── costmap_common_params.yaml  # Configuração comum de costmaps
│   ├── dwa_local_planner_params.yaml
│   ├── global_costmap_params.yaml
│   └── local_costmap_params.yaml
├── launch/                          # Arquivos de inicialização
│   ├── mapeamento.launch           # Launch para modo mapeamento
│   ├── navegacao_remota.launch     # Launch para navegação remota
│   └── robot.launch                # Launch base do robô
├── maps/                            # Mapas do ambiente
│   ├── mapa_laboratorio.pgm        # Imagem do mapa
│   └── mapa_laboratorio.yaml       # Metadados do mapa
├── rviz/                            # Configurações de visualização
│   ├── mapeamento.rviz             # Layout RVIZ para mapeamento
│   └── navegacao.rviz              # Layout RVIZ para navegação
├── CMakeLists.txt
├── package.xml
└── README.md                        # Este arquivo
```

## 🔧 Dependências

### Pré-requisitos de Hardware
Este projeto foi desenvolvido para funcionar com o robô Pioneer 3DX. Antes de executar este pacote, certifique-se de que os seguintes pacotes estão em execução no robô:

- **rosaria** - Pacote de comunicação com o robô Pioneer 3DX
  ```bash
  rosrun rosaria RosAria _port:=/dev/ttyS0 _baud:=9600
  ```

- **sicktoolbox_wrapper** - Pacote para integração do laser Sick
  ```bash
  rosrun sicktoolbox_wrapper sicklms _port:=/dev/ttyS1
  ```

⚠️ **Importante**: Estes pacotes devem estar em execução no robô (ou acessíveis via rede ROS) antes de iniciar qualquer launch file deste projeto.

### Dependências ROS (Catkin)
- `geometry_msgs` - Mensagens de geometria (posição, orientação)
- `rospy` - Cliente Python para ROS

### Ferramentas e Pacotes Adicionais
- **AMCL** - Localização Monte Carlo Adaptativa
- **DWA Local Planner** - Planejador local de trajetória
- **Move Base** - Framework de navegação autônoma
- **RVIZ** - Ferramenta de visualização 3D

## 📦 Instalação

### Pré-requisitos
- ROS instalado (testado em Kinetic ou superior)
- Catkin build system
- Python 2.7+ ou Python 3.x (conforme versão do ROS)

### Passos de Instalação

1. **Clone ou copie o projeto para seu workspace ROS:**
   ```bash
   cd ~/catkin_ws/src
   cp -r /caminho/para/trabalho_slam .
   ```

2. **Instale as dependências:**
   ```bash
   cd ~/catkin_ws
   rosdep install --from-paths src --ignore-src -r -y
   ```

3. **Compile o projeto:**
   ```bash
   catkin_make
   ```

4. **Ative o workspace:**
   ```bash
   source ~/catkin_ws/devel/setup.bash
   ```

## 🚀 Como Usar

### Modo Mapeamento
Para criar um novo mapa do ambiente:

```bash
roslaunch trabalho_slam mapeamento.launch
```

Este comando inicia:
- O robô e sensores
- Ferramentas de mapeamento (ex: gmapping)
- Interface RVIZ pré-configurada para mapeamento

### Modo Navegação Remota
Para navegar usando um mapa pré-existente:

```bash
roslaunch trabalho_slam navegacao_remota.launch
```

Este comando inicia:
- O robô com o mapa carregado
- AMCL para localização
- Move Base para planejamento de caminho
- Interface RVIZ pré-configurada para navegação

### Launch Base do Robô
Para apenas inicializar o robô sem mapeamento ou navegação:

```bash
roslaunch trabalho_slam robot.launch
```

## ⚙️ Configuração

### Arquivos de Configuração Principais

#### `amcl_params.yaml`
Contém os parâmetros do algoritmo AMCL:
- Número de partículas
- Taxa de atualização
- Modelos de sensor e movimento

#### `dwa_local_planner_params.yaml`
Configura o planejador local DWA (Dynamic Window Approach):
- Limites de velocidade
- Parâmetros de aceleração
- Fatores de custo

#### `costmap_common_params.yaml`
Configuração comum para ambos os costmaps:
- Tamanho e resolução
- Limites de obstáculos
- Plugins utilizados

#### Mapas
O mapa do laboratório está localizado em `maps/`:
- **mapa_laboratorio.pgm**: Imagem raster do mapa (branco = livre, preto = obstáculo)
- **mapa_laboratorio.yaml**: Metadados do mapa (resolução, origem, etc.)

## 📊 Visualização com RVIZ

O projeto inclui dois layouts RVIZ pré-configurados:

- **mapeamento.rviz**: Otimizado para visualizar o processo de mapeamento
- **navegacao.rviz**: Otimizado para visualizar navegação e localização

Para carregar um layout manualmente:
```bash
rviz -d $(rospack find trabalho_slam)/rviz/navegacao.rviz
```

## 📝 Workflow Típico

1. **Primeira Execução**: Executar em modo mapeamento para criar um mapa do ambiente
2. **Armazenar Mapa**: Salvar o mapa gerado em `maps/`
3. **Navegar**: Usar o mapa armazenado para navegação autônoma
4. **Ajustar**: Refinar parâmetros de navegação conforme necessário

## 🔍 Troubleshooting

### Problema: Robô não localiza
- Verifique se o mapa carregado corresponde ao ambiente atual
- Ajuste os parâmetros de AMCL em `config/amcl_params.yaml`
- Aumente o número de partículas se necessário

### Problema: Planejamento de caminho lento
- Verifique os parâmetros em `dwa_local_planner_params.yaml`
- Ajuste os limites de velocidade
- Verifique se o mapa de custos está sendo gerado corretamente

### Problema: RVIZ não carrega
- Confirme que o workspace foi compilado corretamente
- Verifique se o ROS_MASTER_URI está configurado corretamente
- Rode `roscore` em um terminal separado

## 📚 Referências

- [ROS Documentation](http://wiki.ros.org/)
- [AMCL Documentation](http://wiki.ros.org/amcl)
- [Move Base Documentation](http://wiki.ros.org/move_base)
- [DWA Local Planner](http://wiki.ros.org/dwa_local_planner)

## 👤 Autor

**Desenvolvedores:**
- João Vitor Barbosa Pinheiro
- Henrique Xavier Vincentini
- Júlia da Cruz Viana

**Professor Orientador:**
- Guilherme de Souza Basto

## 📄 Licença

Este projeto é parte do trabalho acadêmico da disciplina de Robótica Avançada da UNIFEI.

---

**Última atualização**: Dezembro de 2025
