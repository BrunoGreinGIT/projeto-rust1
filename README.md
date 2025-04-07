main

use actix_web::{web, App, HttpResponse, HttpServer, Responder};
use serde::{Deserialize, Serialize};

// Estrutura do Produto
#[derive(Debug, Serialize, Deserialize, Clone)]
struct Produto {
    id: u64,
    nome: String,
    marca: String,
    categoria: String,
    preco: f64,
    descricao: String,
}

// Simulação de um banco de dados (lista estática)
fn obter_produtos() -> Vec<Produto> {
    vec![
        Produto {
            id: 1,
            nome: "Smartphone XYZ".to_string(),
            marca: "TechBrand".to_string(),
            categoria: "Eletrônicos".to_string(),
            preco: 2999.99,
            descricao: "Smartphone com câmera de 108MP e tela AMOLED.".to_string(),
        },
        Produto {
            id: 2,
            nome: "Notebook Pro".to_string(),
            marca: "CompTech".to_string(),
            categoria: "Computadores".to_string(),
            preco: 5999.99,
            descricao: "Notebook com processador i9 e 32GB de RAM.".to_string(),
        },
    ]
}
// Função para buscar produtos pelo nome
async fn buscar_produtos(query: web::Query<SearchQuery>) -> impl Responder {
    let produtos = obter_produtos();

    // Filtrando produtos que contêm o termo de busca no nome
    let resultados: Vec<Produto> = produtos
        .into_iter()
        .filter(|p| p.nome.to_lowercase().contains(&query.nome.to_lowercase()))
        .collect();

    // Retorna JSON com os produtos encontrados
    HttpResponse::Ok().json(resultados)
}

// Estrutura para receber a consulta
#[derive(Deserialize)]
struct SearchQuery {
    nome: String,
}

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    println!("🚀 Servidor rodando em http://127.0.0.1:8080");

    HttpServer::new(|| {
        App::new()
            .route("/buscar", web::get().to(buscar_produtos)) // Endpoint de busca
    })
    .bind("127.0.0.1:8080")?
    .run()
    .await
}



Cargo.toml

[package]
name = "projeto-rust"
version = "0.1.0"
edition = "2024"

[dependencies]
actix-web = "4"    # Framework web para API REST
tokio = { version = "1.0", features = ["full"] }  # Para assíncrono
serde = { version = "1.0", features = ["derive"] }  # Para serialização JSON
serde_json = "1.0"  # Para manipulação de JSON
tantivy = "0.19"  # Para indexação e busca full-text



