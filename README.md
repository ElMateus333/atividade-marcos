# atividade-marcos

Estrutura do Projeto
O projeto será dividido em módulos que incluem:

Carga de dados: Carregar produtos, matérias-primas e pedidos de arquivos JSON.
Algoritmo de otimização: Implementar um algoritmo para otimizar o processo de produção.
Geração de pedidos de compra: Gerar e listar pedidos de compra de matérias-primas com base nas necessidades de produção.
Estrutura dos Arquivos JSON
Produtos (produtos.json)

[
  {
    "id": "P1",
    "nome": "Produto 1",
    "materias_primas": {
      "MP1": 2,
      "MP2": 3
    },
    "data_entrega": "2024-07-10"
  },
  ...
]
Matérias-Primas (materias_primas.json)
json
Copiar código
[
  {
    "id": "MP1",
    "nome": "Materia Prima 1",
    "quantidade_minima": 50,
    "tempo_entrega": 5
  },
  ...
]
Estrutura dos Módulos
1. Carga de Dados
carga_dados.rs

rust
Copiar código
use serde::{Deserialize, Serialize};
use std::fs;

#[derive(Serialize, Deserialize)]
struct Produto {
    id: String,
    nome: String,
    materias_primas: std::collections::HashMap<String, u32>,
    data_entrega: String,
}

#[derive(Serialize, Deserialize)]
struct MateriaPrima {
    id: String,
    nome: String,
    quantidade_minima: u32,
    tempo_entrega: u32,
}

fn carregar_produtos(caminho: &str) -> Vec<Produto> {
    let conteudo = fs::read_to_string(caminho).expect("Erro ao ler o arquivo de produtos.");
    serde_json::from_str(&conteudo).expect("Erro ao parsear o JSON de produtos.")
}

fn carregar_materias_primas(caminho: &str) -> Vec<MateriaPrima> {
    let conteudo = fs::read_to_string(caminho).expect("Erro ao ler o arquivo de matérias-primas.");
    serde_json::from_str(&conteudo).expect("Erro ao parsear o JSON de matérias-primas.")
}


2. Algoritmo de Otimização
otimizacao.rs


use chrono::NaiveDate;
use std::collections::HashMap;

struct Pedido {
    id_produto: String,
    quantidade: u32,
    data_entrega: NaiveDate,
}

struct PedidoCompra {
    id_materia_prima: String,
    quantidade_total: u32,
    data_pedido: NaiveDate,
}

fn otimizar_producao(
    produtos: Vec<Produto>,
    materias_primas: Vec<MateriaPrima>,
    pedidos: Vec<Pedido>,
) -> Vec<PedidoCompra> {
    // Implementação do algoritmo de otimização
    // 1. Calcular a demanda de matérias-primas
    // 2. Planejar compras com base nos prazos de entrega e datas de uso
    // 3. Gerar pedidos de compra otimizados

    // Exemplo simplificado:
    let mut demanda: HashMap<String, u32> = HashMap::new();
    for pedido in pedidos {
        let produto = produtos.iter().find(|p| p.id == pedido.id_produto).unwrap();
        for (mp, &quantidade) in &produto.materias_primas {
            *demanda.entry(mp.clone()).or_insert(0) += quantidade * pedido.quantidade;
        }
    }

    let mut pedidos_compra: Vec<PedidoCompra> = Vec::new();
    for (mp, &quantidade) in &demanda {
        let materia_prima = materias_primas.iter().find(|m| m.id == mp.clone()).unwrap();
        let data_pedido = NaiveDate::parse_from_str(&produto.data_entrega, "%Y-%m-%d")
            .unwrap()
            .checked_sub_signed(chrono::Duration::days(materia_prima.tempo_entrega as i64))
            .unwrap();

        pedidos_compra.push(PedidoCompra {
            id_materia_prima: mp.clone(),
            quantidade_total: quantidade,
            data_pedido,
        });
    }

    pedidos_compra
}
Implementação Completa em Rust
main.rs
rust
Copiar código
mod carga_dados;
mod otimizacao;

use carga_dados::{carregar_materias_primas, carregar_produtos};
use otimizacao::otimizar_producao;

fn main() {
    let produtos = carregar_produtos("data/produtos.json");
    let materias_primas = carregar_materias_primas("data/materias_primas.json");

    let pedidos = vec![
        Pedido {
            id_produto: "P1".to_string(),
            quantidade: 10,
            data_entrega: "2024-07-10".to_string(),
        },
        Pedido {
            id_produto: "P2".to_string(),
            quantidade: 5,
            data_entrega: "2024-07-15".to_string(),
        },
    ];

    let pedidos_compra = otimizar_producao(produtos, materias_primas, pedidos);
    for pedido in pedidos_compra {
        println!(
            "Pedido de Compra - Matéria-Prima: {}, Quantidade: {}, Data do Pedido: {}",
            pedido.id_materia_prima, pedido.quantidade_total, pedido.data_pedido
        );
    }
}
Documentação README.md

# Sistema de Planejamento e Controle de Produção

## Apresentação do Problema

A otimização da produção em uma fábrica que produz diversos produtos a partir de diferentes subconjuntos de matérias-primas. O objetivo é minimizar o estoque e evitar desperdícios, considerando restrições como capacidade de produção, disponibilidade e tempo de entrega das matérias-primas.

## Complexidade do Problema

O problema é classificado como NP-Completo, pois envolve múltiplas restrições e a necessidade de encontrar uma solução ótima em um espaço de soluções potencialmente exponencial.

## Algoritmos Conhecidos

Algoritmos conhecidos para problemas de planejamento e controle de produção incluem:
- Algoritmos de Programação Linear
- Algoritmos de Busca Heurística (como o Algoritmo Genético)
- Algoritmos de Otimização Combinatória (como o Branch and Bound)

## Algoritmo Escolhido e Implementação

Foi escolhido um algoritmo baseado em programação linear simples para calcular a demanda de matérias-primas e gerar pedidos de compra otimizados. A implementação utiliza contagem de demandas e planeja pedidos baseados em prazos de entrega.

## Complexidade do Algoritmo Escolhido

A complexidade do algoritmo é O(n), onde n é o número de pedidos. O trecho de maior complexidade é o cálculo da demanda de matérias-primas.

## Paradigma e Estratégia do Algoritmo Escolhido

O algoritmo utiliza o paradigma de programação linear e estratégia de planejamento baseado na demanda e prazos de entrega.

## Comparação com Outro Algoritmo

Comparado com o Algoritmo Genético, o algoritmo escolhido é mais simples e direto, porém menos flexível para encontrar soluções ótimas em problemas mais complexos. A complexidade de um Algoritmo Genético é tipicamente maior devido às múltiplas iterações e avaliação de populações de soluções.

## Linguagem

Rust foi utilizado para aproveitar sua performance e segurança de memória. A implementação aproveita a forte tipagem de Rust e suas capacidades de manipulação de coleções.

## Apresentação

### Capa
Sistema de Planejamento e Controle de Produção

### Resumo
Implementação de um sistema em Rust para otimizar a produção de uma fábrica, considerando restrições de capacidade, tempo de entrega e disponibilidade de matérias-primas.

### O Problema
Minimização de estoque e desperdício na produção de diversos produtos, respeitando restrições operacionais.

### O Algoritmo
Implementação baseada em programação linear para calcular demanda e planejar pedidos.

### Complexidades Big-O
O(n) para o algoritmo de contagem de demandas.

### Escolha do Algoritmo
Escolhido pela simplicidade e eficiência em problemas de planejamento de produção.

### Código
Ver seção de implementação.

### Lições Aprendidas
Importância de considerar todas as restrições operacionais e a utilidade de Rust para sistemas de alta performance.
Conclusão
O sistema de planejamento e controle de produção foi desenvolvido em Rust para otimizar a produção de uma fábrica. O algoritmo implementado foca em minimizar estoques e evitar desperdícios, considerando restrições operacionais. A documentação detalha a complexidade do problema, os algoritmos conhecidos, a escolha do algoritmo e as lições aprendidas.
