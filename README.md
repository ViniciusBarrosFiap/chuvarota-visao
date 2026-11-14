# Visão computacional: nível de água em ônibus alagados

Vinicius Barros, RM 97824 (FIAP, Global Solution 2026, Applied Computer Vision)

Treinei um modelo (YOLOv8) que acha ônibus em rua alagada e diz até onde a água chega, em 4 níveis. No ChuvaRota ele serve para transformar uma foto da rua em um aviso ("não atravesse"), junto com a previsão de chuva.

## Dados
QIU, Yanbin (2025). [Automated urban flood level detection based on flooded bus dataset wading image using YOLOv8](https://doi.org/10.5281/zenodo.17151262). Zenodo, licença CC BY 4.0 (imagens de redes sociais anotadas no LabelImg). Usei só o `Original.zip`, sem mexer nele; as imagens de `amostras/` vêm dele. 1.008 imagens, 4 classes:

| classe | água no ônibus |
|---|---|
| `nivel1_0a20cm` | 0 a 20 cm |
| `nivel2_20a45cm` | 20 a 45 cm |
| `nivel3_45a100cm` | 45 a 100 cm |
| `nivel4_acima_100cm` | mais de 100 cm |

O treino original (900) virou 810 de treino e 90 de validação (sorteados); as 108 imagens de validação originais viraram o **teste**, que o modelo nunca viu.

## Resultado (teste: 108 imagens que o modelo nunca viu)
| | modelo treinado | YOLOv8n do COCO |
|---|---|---|
| acha o ônibus alagado (recall) | 71% | 50% |
| precisão | 75% | 72% |
| nível da água exato | 76% | não estima |
| nível com erro de no máximo 1 | 98% | não estima |

Cada foto leva uns 13 ms para ser analisada. Os detalhes e os limites (fotos de outro país, só acha ônibus, o nível 4 é fraco) estão no [relatorio_visao.pdf](relatorio_visao.pdf).

## O que tem aqui
- `preparar_dados.py`: separa as imagens em treino, validação e teste
- `treinar.py`: treina o modelo (YOLOv8n, partindo dos pesos do COCO)
- `avaliar.py`: mede o resultado no teste e compara com o modelo original
- `gerar_amostras.py`: gera as imagens de exemplo
- `detectar.py`: roda o modelo em uma foto, vídeo ou na webcam
- `api.py`: API que recebe uma foto e responde o nível da água
- `modelo/best.pt`: o modelo treinado
- `avaliacao/`: métricas e gráficos
- `amostras/`: exemplos de entrada e saída, e o vídeo

## Como rodar
```
pip install -r requirements.txt
# baixe o Original.zip do Zenodo (link lá em cima) e extraia em dados_brutos/bus/
python preparar_dados.py
python treinar.py
python avaliar.py
python detectar.py foto.jpg --saida resultado     # foto
python detectar.py video.mp4 --saida resultado    # vídeo
python detectar.py 0                              # webcam (q para sair)
uvicorn api:app --port 8002                       # API: POST /analisar com o campo "foto"
```

## Links
- Código: https://github.com/ViniciusBarrosFiap/chuvarota-visao
- Vídeo: https://github.com/ViniciusBarrosFiap/chuvarota-visao/blob/main/amostras/video_visao.mp4
- Relatório: https://github.com/ViniciusBarrosFiap/chuvarota-visao/blob/main/relatorio_visao.pdf
