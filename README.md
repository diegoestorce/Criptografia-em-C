# Criptografia com Progressões e Séries em C

## 1. Descrição

Programa em C que une **criptografia simples**, **matemática aplicada (progressões e séries)** e conceitos computacionais (arquivos, menus, validação de entrada, funções e log de execução). A atividade é organizada segundo a **Taxonomia de Bloom**.

A palavra secreta passa por duas camadas de cifragem:

| Camada | Técnica | Deslocamento |
|--------|---------|--------------|
| 1 | Cifra de César | SHIFT fixo escolhido pelo aluno |
| 2 | Deslocamento dinâmico | Termo `i` da sequência numérica (PA, PG, Fibonacci ou primos) |

Deslocamento total da letra `i`:

```
deslocamento[i] = (SHIFT + sequencia[i]) mod 26
letra_cifrada[i] = (letra[i] + deslocamento[i]) mod 26
```

## 2. Como compilar e executar

```bash
gcc -Wall -o criptografia criptografia.c
./criptografia
```

Menu do programa:

1. **Criptografar palavra**: lê palavra (até 15 letras, sem acentos nem especiais), SHIFT (1 a 25) e a sequência; mostra o passo a passo, grava o resultado e registra o log.
2. **Descriptografar palavra**: desfaz as duas camadas, usando o mesmo SHIFT e a mesma sequência.
3. **Comparar sequências**: cifra a mesma palavra com PA, PG, Fibonacci e primos e compara os resultados.
0. **Sair**

## 3. Arquivos gerados

**`resultado_criptografia.txt`** (formato do enunciado):

```
Palavra codificada: gswgkle | SHIFT: 3 | Tipo: 3 | Letras: 7
```

O campo `Tipo` segue o menu: 1 = PA, 2 = PG, 3 = Fibonacci, 4 = Primos.

**`log_execucao.txt`**: uma linha por ação, com data e hora, palavra, SHIFT, sequência, parâmetros e resultado.

## 4. Fundamentos matemáticos

| Técnica | Fórmula | Exemplo |
|---------|---------|---------|
| Progressão Aritmética | `aₙ = a₁ + (n-1)·r` ; `Sₙ = (a₁+aₙ)·n/2` | 1, 3, 5, 7, ... (a₁=1, r=2) |
| Progressão Geométrica | `aₙ = a₁·qⁿ⁻¹` | 1, 2, 4, 8, ... (a₁=1, q=2) |
| Fibonacci | `Fₙ = Fₙ₋₁ + Fₙ₋₂`, `F₁=F₂=1` | 1, 1, 2, 3, 5, 8, 13 |
| Números primos | sem fórmula geral (teste por divisão) | 2, 3, 5, 7, 11, 13, 17 |

Como os termos crescem rápido (principalmente na PG), o programa usa `long long` e aplica `mod 26` na hora de cifrar. Os parâmetros são limitados (a₁ de 1 a 100, razão de 1 a 10) para evitar estouro.

## 5. Exemplo de execução

Entrada: palavra `coracao`, SHIFT `3`, sequência Fibonacci.

| Letra | Fibonacci | Deslocamento (3 + Fᵢ) | Cifrada |
|-------|-----------|-----------------------|---------|
| c | 1 | 4 | g |
| o | 1 | 4 | s |
| r | 2 | 5 | w |
| a | 3 | 6 | g |
| c | 5 | 8 | k |
| a | 8 | 11 | l |
| o | 13 | 16 | e |

Saída: **`gswgkle`**

> **Observação sobre o enunciado:** o exemplo do enunciado informa `fqvdjqb`. Aplicando a regra descrita (`SHIFT + sequência[i]`), o resultado correto é `gswgkle`, que é o que o programa produz. A diferença deve ser confirmada com o professor.

## 6. Análise comparativa (Taxonomia de Bloom: Analisar)

Foram usados SHIFT = 3, PA com a₁=1 e r=2, PG com a₁=1 e q=2. As métricas são:

- **Deslocamentos distintos:** quantos valores diferentes de deslocamento foram usados (quanto mais, menos previsível).
- **Vazamentos:** pares de letras iguais no texto original que continuam iguais na palavra cifrada (revelam padrão de frequência; o ideal é zero).

| Palavra | Sequência | Cifrada | Desloc. distintos | Vazamentos |
|---------|-----------|---------|-------------------|------------|
| coracao (7) | PA | guzkooe | 7 | 0 |
| | PG | gtylvjd | 7 | 0 |
| | Fibonacci | gswgkle | 6 | 0 |
| | Primos | huzkqqi | 7 | 0 |
| banana (6) | PA | fgvkzo | 6 | 0 |
| | PG | ffulgj | 6 | 0 |
| | Fibonacci | fesgvl | 5 | 0 |
| | Primos | ggvkbq | 6 | 0 |
| abracadabra (11) | PA | ehzkootsvny | 11 | 0 |
| | PG | egylvjsbamn | 11 | 0 |
| | Fibonacci | efwgkltymxo | 8 | 0 |
| | Primos | fhzkqqxwbxi | 9 | 0 |

**Leitura dos resultados**

- **Fibonacci** teve sempre o menor número de deslocamentos distintos. Os dois primeiros termos são iguais (F₁ = F₂ = 1), e, ao reduzir módulo 26, outros valores também voltam a se repetir.
- **PA e PG** tiveram o máximo de deslocamentos distintos nas palavras testadas, mas ambas ciclam: com razão 2, os valores módulo 26 se repetem a cada 13 termos (PA) e por volta do 13º termo (PG). Em palavras de até 15 letras isso já aparece.
- **Primos** ficaram no meio-termo: alguns primos coincidem módulo 26 (por exemplo, 3 e 29), mas não há fórmula simples que revele o padrão.
- **Vazamentos foram zero em todos os casos**, porque o deslocamento muda a cada posição. É a principal vantagem da camada 2 sobre a Cifra de César pura, em que "a" repetido sempre vira a mesma letra.

## 7. Avaliação (Taxonomia de Bloom: Avaliar)

**Qual sequência produz a criptografia mais forte?** Depende do critério:

- Pelo número de deslocamentos distintos, a **PA com razão coprima de 26** (por exemplo, r = 3, 5 ou 7) é a melhor: ela percorre os 26 valores possíveis antes de repetir. Com r = 2, como nos testes, só há 13 valores diferentes.
- Pela dificuldade de adivinhar o padrão, os **números primos** levam vantagem, porque não seguem fórmula fechada.
- **Fibonacci** é a mais fraca nos testes, por causa das repetições iniciais.

**Limitações importantes (pensamento crítico):**

1. A cifra continua sendo uma substituição com deslocamento, no mesmo espírito da cifra de Vigenère. Não é segura para uso real.
2. A segurança depende de manter secretos o SHIFT, a sequência e seus parâmetros. As sequências são determinísticas: quem conhece o tipo e os parâmetros reproduz a chave inteira.
3. Há poucas combinações possíveis (25 shifts × 4 sequências × parâmetros pequenos), então um ataque de força bruta testa todas rapidamente.
4. Com um par texto original/cifrado conhecido, é trivial recuperar os deslocamentos e identificar a sequência.

Conclusão sugerida: a matemática aplicada aumenta a variação entre as letras e elimina padrões de repetição simples, mas não transforma a cifra em um sistema seguro. Criptografia real usa algoritmos como AES, com chaves grandes e análise formal.




