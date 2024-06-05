# Trabalho Fundamentos de sistemas de informação

### Escolha de um Domínio de Aplicação

**Domínio de aplicação:** Gestão de Ativos de TI na Empresa

### Definição de Três Indicadores de Performance

#### Indicador 1: Percentual de Máquinas Alocadas por Unidade

**Variáveis:**

- Número total de máquinas (TM)
- Número de máquinas por unidade (MU)

**Fórmula:** MU / TM

### Indicador 2: Percentual de Usuários com Office Atualizado

**Variáveis:**

- Número total de usuários (TU)
- Número de usuários com Office atualizado (UO)

**Fórmula:** UO / TU

### Indicador 3: Distribuição de Tipos de Máquinas por Unidade

**Variáveis:**

- Número total de máquinas em uma unidade (MU)
- Número de máquinas de um tipo específico na unidade (MT)

**Fórmula:** MT / MU

## c) Diagrama do Modelo Dimensional para Cada Indicador

### Indicador 1: Percentual de Máquinas Alocadas por Unidade

**Fato:** Maquinas

**Medida:** Contagem de máquinas

**Dimensões:**

- **Unidade**
  - UnidadeID
  - NomeUnidade
  - Localizacao

### Indicador 2: Percentual de Usuários com Office Atualizado

**Fato:** Usuarios

**Medida:** Contagem de usuários com Office atualizado

**Dimensões:**

- **Usuario**
  - UsuarioID
  - NomeUsuario
  - Email
  - UnidadeID
- **Office**
  - OfficeID
  - NomeOffice
  - Versao
  - MaquinaID
  - Ano

### Indicador 3: Distribuição de Tipos de Máquinas por Unidade

**Fato:** Maquinas

**Medida:** Contagem de máquinas por tipo

**Dimensões:**

- **Unidade**
  - UnidadeID
  - NomeUnidade
  - Localizacao
- **Maquina**
  - MaquinaID
  - NomeMaquina
  - Tipo
  - UsuarioID
  - SerialMaquina

**1. Percentual de Máquinas Alocadas por Unidade**

```text
┌──────────────┐
│  dim_unidade │
│──────────────│
│ id_unidade   │
│ nm_unidade   │
│ localizacao  │
└─────┬────────┘
      │
      │
      ▼
┌─────────────────┐
│  fato_maquinas  │
│─────────────────│
│ id_maquina (FK) │
│ id_unidade (FK) │
│ id_tempo (FK)   │
└─────┬───────────┘
      │
      │
      ▼
┌───────────────┐
│  dim_maquina  │
│───────────────│
│ id_maquina    │
│ nm_maquina    │
│ tp_maquina    │
│ id_usuario (FK)│
│ id_unidade (FK)│
│ serial_maquina │
└───────────────┘
```

**2. Percentual de Usuários com Office Atualizado**

```text
┌──────────────┐
│  dim_unidade │
│──────────────│
│ id_unidade   │
│ nm_unidade   │
│ localizacao  │
└─────┬────────┘
      │
      │
      ▼
┌─────────────────┐
│  fato_office    │
│─────────────────│
│ id_office       │
│ nm_office       │
│ versao          │
│ id_maquina (FK) │
│ ano             │
└─────┬───────────┘
      │
      │
      ▼
┌───────────────┐
│  dim_maquina  │
│───────────────│
│ id_maquina    │
│ nm_maquina    │
│ tp_maquina    │
│ id_usuario (FK)│
│ id_unidade (FK)│
│ serial_maquina │
└───────────────┘

```

**3. Distribuição de Tipos de Máquinas por Unidade**

```text
┌──────────────┐
│  dim_unidade │
│──────────────│
│ id_unidade   │
│ nm_unidade   │
│ localizacao  │
└─────┬────────┘
      │
      │
      ▼
┌─────────────────┐
│  fato_maquinas  │
│─────────────────│
│ id_maquina (FK) │
│ id_unidade (FK) │
│ id_tempo (FK)   │
└─────┬───────────┘
      │
      │
      ▼
┌───────────────┐
│  dim_maquina  │
│───────────────│
│ id_maquina    │
│ nm_maquina    │
│ tp_maquina    │
│ id_usuario (FK)│
│ id_unidade (FK)│
│ serial_maquina │
└───────────────┘

```

#### Script de Criação da Tabela

```sql
-- Criar banco de dados
CREATE DATABASE IF NOT EXISTS BI_EmpresaDB;

-- Usar o banco de dados recém-criado
USE BI_EmpresaDB;

-- Criar tabela Dimensão Unidade
CREATE TABLE IF NOT EXISTS dim_unidade (
    id_unidade VARCHAR(255) PRIMARY KEY,
    nm_unidade VARCHAR(255),
    localizacao VARCHAR(255)
);

-- Criar tabela Dimensão Usuário
CREATE TABLE IF NOT EXISTS dim_usuario (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    nm_usuario VARCHAR(255),
    email VARCHAR(255),
    id_unidade VARCHAR(255),
    FOREIGN KEY (id_unidade) REFERENCES dim_unidade(id_unidade)
);

-- Criar tabela Dimensão Tempo
CREATE TABLE IF NOT EXISTS dim_tempo (
    id_tempo INT AUTO_INCREMENT PRIMARY KEY,
    ano INT,
    mes INT,
    dia INT
);

-- Criar tabela Dimensão Maquina
CREATE TABLE IF NOT EXISTS dim_maquina (
    id_maquina INT AUTO_INCREMENT PRIMARY KEY,
    nm_maquina VARCHAR(255),
    tp_maquina VARCHAR(255),
    id_usuario INT,
    id_unidade VARCHAR(255),
    serial_maquina VARCHAR(255),
    FOREIGN KEY (id_usuario) REFERENCES dim_usuario(id_usuario),
    FOREIGN KEY (id_unidade) REFERENCES dim_unidade(id_unidade)
);

-- Criar tabela Fato Maquinas
CREATE TABLE IF NOT EXISTS fato_maquinas (
    id_maquina INT,
    id_unidade VARCHAR(255),
    id_tempo INT,
    FOREIGN KEY (id_maquina) REFERENCES dim_maquina(id_maquina),
    FOREIGN KEY (id_unidade) REFERENCES dim_unidade(id_unidade),
    FOREIGN KEY (id_tempo) REFERENCES dim_tempo(id_tempo)
);

-- Criar tabela Fato Office
CREATE TABLE IF NOT EXISTS fato_office (
    id_office INT AUTO_INCREMENT PRIMARY KEY,
    nm_office VARCHAR(255),
    versao VARCHAR(255),
    id_maquina INT,
    ano INT,
    FOREIGN KEY (id_maquina) REFERENCES dim_maquina(id_maquina)
);

-- Inserir dados nas tabelas Dimensão
INSERT INTO dim_unidade (id_unidade, nm_unidade, localizacao) VALUES
('UEMJU01', 'Unidade A', 'Local A'),
('UEMJU02', 'Unidade B', 'Local B');

INSERT INTO dim_usuario (nm_usuario, email, id_unidade) VALUES
('railane.santos', 'railane.santos@empresa.com', 'UEMJU01'),
('maria.santos', 'maria.santos@empresa.com', 'UEMJU02');

INSERT INTO dim_tempo (ano, mes, dia) VALUES
(2023, 1, 1),
(2023, 1, 2);

INSERT INTO dim_maquina (nm_maquina, tp_maquina, id_usuario, id_unidade, serial_maquina) VALUES
('E470', 'Notebook', 1, 'UEMJU01', 'PE02NVD3'),
('E570', 'Desktop', 2, 'UEMJU02', 'PE03NVD4');

-- Inserir dados na tabela Fato Maquinas
INSERT INTO fato_maquinas (id_maquina, id_unidade, id_tempo) VALUES
(1, 'UEMJU01', 1),
(2, 'UEMJU02', 2);

-- Inserir dados na tabela Fato Office
INSERT INTO fato_office (nm_office, versao, id_maquina, ano) VALUES
('Office 2019', '10', 1, 2019),
('Office 2021', '11', 2, 2021);

```

##### 1. Percentual de Máquinas Alocadas por Unidade

```sql
SELECT 
    d.nm_unidade, 
    m.tp_maquina, 
    COUNT(m.id_maquina) AS total_maquinas, 
    (COUNT(m.id_maquina) / (SELECT COUNT(*) FROM dim_maquina)) * 100 AS percentual_tipo
FROM 
    fato_maquinas fm
JOIN 
    dim_maquina m ON fm.id_maquina = m.id_maquina
JOIN 
    dim_unidade d ON fm.id_unidade = d.id_unidade
GROUP BY 
    d.nm_unidade, m.tp_maquina;

```

#### 2.Percentual de Usuários com Office Atualizado

```sql
SELECT 
    d.nm_unidade, 
    COUNT(DISTINCT u.id_usuario) AS total_usuarios, 
    COUNT(DISTINCT fo.id_maquina) AS usuarios_com_office_atualizado, 
    (COUNT(DISTINCT fo.id_maquina) / COUNT(DISTINCT u.id_usuario)) * 100 AS percentual_atualizado
FROM 
    dim_usuario u
JOIN 
    dim_maquina m ON u.id_usuario = m.id_usuario
JOIN 
    fato_office fo ON m.id_maquina = fo.id_maquina
JOIN 
    dim_unidade d ON u.id_unidade = d.id_unidade
GROUP BY 
    d.nm_unidade;

```

#### 3. Distribuição de Tipos de Máquinas por Unidade

```sql
SELECT 
    d.nm_unidade, 
    m.tp_maquina, 
    COUNT(m.id_maquina) AS total_maquinas,
    (COUNT(m.id_maquina) / (SELECT COUNT(*) FROM dim_maquina)) * 100 AS percentual_maquinas
FROM 
    dim_maquina m
JOIN 
    dim_unidade d ON m.id_unidade = d.id_unidade
GROUP BY 
    d.nm_unidade, m.tp_maquina;

```

#### teste usando mysql e phpmyadmin
![image](https://github.com/einstein808/FundamentosSI/assets/79322072/5c791dad-977c-4e29-8c79-3c4c656f33f0)


**1. Teste indicador 1**
![image](https://github.com/einstein808/FundamentosSI/assets/79322072/64746a57-f42c-40da-9e8c-3407b28fa921)

**2. teste Indicador 2**
![image](https://github.com/einstein808/FundamentosSI/assets/79322072/142fd691-cd4b-47f3-a240-fcb1e176e222)


**3. teste Indicador 3**
![image](https://github.com/einstein808/FundamentosSI/assets/79322072/6022777e-18f5-49d0-9391-105c1f6185b2)



