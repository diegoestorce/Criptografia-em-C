#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <time.h>
#include <stdarg.h>

#define MAX_LETRAS 15
#define ARQ_RESULTADO "resultado_criptografia.txt"
#define ARQ_LOG "log_execucao.txt"

enum { PA = 1, PG = 2, FIBONACCI = 3, PRIMOS = 4 };

/* ---------- Utilitarios ---------- */

const char *nome_sequencia(int tipo) {
    switch (tipo) {
        case PA:        return "Progressao Aritmetica";
        case PG:        return "Progressao Geometrica";
        case FIBONACCI: return "Fibonacci";
        case PRIMOS:    return "Numeros Primos";
        default:        return "Desconhecida";
    }
}

int mod26(long long x) {
    int r = (int)(x % 26);
    return r < 0 ? r + 26 : r;
}

/* Registra uma linha no arquivo de log com data e hora */
void registrar_log(const char *formato, ...) {
    FILE *f = fopen(ARQ_LOG, "a");
    if (!f) return;

    time_t agora = time(NULL);
    char data[32];
    strftime(data, sizeof(data), "%d/%m/%Y %H:%M:%S", localtime(&agora));
    fprintf(f, "[%s] ", data);

    va_list args;
    va_start(args, formato);
    vfprintf(f, formato, args);
    va_end(args);

    fprintf(f, "\n");
    fclose(f);
}

void limpar_linha(char *s) {
    s[strcspn(s, "\r\n")] = '\0';
}

int ler_inteiro(const char *msg, int min, int max) {
    char buf[64];
    int valor;
    while (1) {
        printf("%s", msg);
        if (!fgets(buf, sizeof(buf), stdin)) exit(0);
        if (sscanf(buf, "%d", &valor) == 1 && valor >= min && valor <= max)
            return valor;
        printf("Valor invalido. Digite um numero entre %d e %d.\n", min, max);
    }
}

/* Le a palavra: ate 15 letras, sem acentos nem caracteres especiais */
void ler_palavra(const char *msg, char *palavra) {
    char buf[128];
    while (1) {
        printf("%s", msg);
        if (!fgets(buf, sizeof(buf), stdin)) exit(0);
        limpar_linha(buf);

        size_t n = strlen(buf);
        int ok = (n >= 1 && n <= MAX_LETRAS);
        for (size_t i = 0; ok && i < n; i++) {
            unsigned char c = (unsigned char)buf[i];
            if (!((c >= 'a' && c <= 'z') || (c >= 'A' && c <= 'Z'))) ok = 0;
        }
        if (ok) {
            for (size_t i = 0; i < n; i++)
                palavra[i] = (char)tolower((unsigned char)buf[i]);
            palavra[n] = '\0';
            return;
        }
        printf("Palavra invalida. Use de 1 a %d letras, sem acentos, espacos ou especiais.\n",
               MAX_LETRAS);
    }
}

/* ---------- Matematica aplicada: progressoes e series ---------- */

int eh_primo(long long n) {
    if (n < 2) return 0;
    for (long long d = 2; d * d <= n; d++)
        if (n % d == 0) return 0;
    return 1;
}

/*
 * Gera os n primeiros termos da sequencia escolhida.
 *  PA:        a_n = a1 + (n-1)*r
 *  PG:        a_n = a1 * q^(n-1)
 *  Fibonacci: F1 = 1, F2 = 1, F_n = F_(n-1) + F_(n-2)
 *  Primos:    2, 3, 5, 7, 11, ...
 * Para PA e PG, "param1" = a1 e "param2" = razao.
 */
void gerar_sequencia(int tipo, int n, long long a1, long long razao, long long *seq) {
    switch (tipo) {
        case PA:
            for (int i = 0; i < n; i++) seq[i] = a1 + (long long)i * razao;
            break;
        case PG:
            seq[0] = a1;
            for (int i = 1; i < n; i++) seq[i] = seq[i - 1] * razao;
            break;
        case FIBONACCI:
            for (int i = 0; i < n; i++)
                seq[i] = (i < 2) ? 1 : seq[i - 1] + seq[i - 2];
            break;
        case PRIMOS: {
            long long candidato = 2;
            for (int i = 0; i < n; i++) {
                while (!eh_primo(candidato)) candidato++;
                seq[i] = candidato++;
            }
            break;
        }
    }
}

/* Soma da PA (formula Sn = (a1 + an) * n / 2) - usada no log como curiosidade */
long long soma_pa(long long a1, long long an, int n) {
    return (a1 + an) * n / 2;
}

/* ---------- Criptografia ---------- */

/*
 * direcao = +1 criptografa, -1 descriptografa.
 * Camada 1 (Cesar) + Camada 2 (sequencia) = deslocamento SHIFT + seq[i].
 */
void processar(const char *texto, int shift, const long long *seq, int direcao, char *saida) {
    size_t n = strlen(texto);
    for (size_t i = 0; i < n; i++) {
        int pos = texto[i] - 'a';
        int camada1 = mod26(pos + direcao * (long long)shift);
        int camada2 = mod26(camada1 + direcao * (seq[i] % 26));
        saida[i] = (char)('a' + camada2);
    }
    saida[n] = '\0';
}

void mostrar_sequencia(const long long *seq, int n) {
    for (int i = 0; i < n; i++)
        printf("%lld%s", seq[i], i < n - 1 ? ", " : "\n");
}

/* Pede os parametros da sequencia escolhida */
void ler_parametros(int tipo, long long *a1, long long *razao) {
    *a1 = 1;
    *razao = 1;
    if (tipo == PA) {
        *a1 = ler_inteiro("Primeiro termo a1 (1 a 100): ", 1, 100);
        *razao = ler_inteiro("Razao r (1 a 10): ", 1, 10);
    } else if (tipo == PG) {
        *a1 = ler_inteiro("Primeiro termo a1 (1 a 100): ", 1, 100);
        *razao = ler_inteiro("Razao q (2 a 10): ", 2, 10);
    }
}

int escolher_sequencia(void) {
    printf("\nSequencias disponiveis:\n");
    printf("  1 - Progressao Aritmetica (PA)\n");
    printf("  2 - Progressao Geometrica (PG)\n");
    printf("  3 - Fibonacci\n");
    printf("  4 - Numeros Primos\n");
    return ler_inteiro("Escolha (1-4): ", 1, 4);
}

/* ---------- Opcoes do menu ---------- */

void opcao_criptografar(void) {
    char palavra[MAX_LETRAS + 1], cifrada[MAX_LETRAS + 1];
    long long seq[MAX_LETRAS], a1, razao;

    ler_palavra("\nPalavra secreta (ate 15 letras): ", palavra);
    int shift = ler_inteiro("SHIFT (1 a 25): ", 1, 25);
    int tipo = escolher_sequencia();
    ler_parametros(tipo, &a1, &razao);

    int n = (int)strlen(palavra);
    gerar_sequencia(tipo, n, a1, razao, seq);
    processar(palavra, shift, seq, +1, cifrada);

    printf("\nSequencia (%s): ", nome_sequencia(tipo));
    mostrar_sequencia(seq, n);
    printf("Camada 1 (Cesar) + Camada 2 (sequencia):\n");
    for (int i = 0; i < n; i++) {
        int total = mod26(shift + seq[i] % 26);
        printf("  %c -> %c  (deslocamento %d + %lld = %d)\n",
               palavra[i], cifrada[i], shift, seq[i], total);
    }
    printf("\nPalavra criptografada: %s\n", cifrada);

    FILE *f = fopen(ARQ_RESULTADO, "w");
    if (f) {
        fprintf(f, "Palavra codificada: %s | SHIFT: %d | Tipo: %d | Letras: %d\n",
                cifrada, shift, tipo, n);
        fclose(f);
        printf("Resultado gravado em %s\n", ARQ_RESULTADO);
    } else {
        printf("Erro ao gravar %s\n", ARQ_RESULTADO);
    }

    registrar_log("CRIPTOGRAFAR | palavra=%s | shift=%d | sequencia=%s | a1=%lld | razao=%lld | resultado=%s",
                  palavra, shift, nome_sequencia(tipo), a1, razao, cifrada);
    if (tipo == PA)
        registrar_log("Soma da PA (S%d) = %lld", n, soma_pa(seq[0], seq[n - 1], n));
}

void opcao_descriptografar(void) {
    char cifrada[MAX_LETRAS + 1], original[MAX_LETRAS + 1];
    long long seq[MAX_LETRAS], a1, razao;

    ler_palavra("\nPalavra criptografada: ", cifrada);
    int shift = ler_inteiro("SHIFT usado (1 a 25): ", 1, 25);
    int tipo = escolher_sequencia();
    ler_parametros(tipo, &a1, &razao);

    int n = (int)strlen(cifrada);
    gerar_sequencia(tipo, n, a1, razao, seq);
    processar(cifrada, shift, seq, -1, original);

    printf("\nPalavra original: %s\n", original);
    registrar_log("DESCRIPTOGRAFAR | cifrada=%s | shift=%d | sequencia=%s | resultado=%s",
                  cifrada, shift, nome_sequencia(tipo), original);
}

/*
 * Analise comparativa (niveis Analisar e Avaliar da Taxonomia de Bloom).
 * Metricas:
 *  - deslocamentos distintos: quantos valores diferentes de deslocamento foram usados
 *    (quanto mais, menos previsivel);
 *  - vazamentos: pares de letras iguais no texto original que continuam iguais
 *    na palavra cifrada (revelam padrao de frequencia; quanto menos, melhor).
 */
void opcao_comparar(void) {
    char palavra[MAX_LETRAS + 1], cifrada[MAX_LETRAS + 1];
    long long seq[MAX_LETRAS];

    ler_palavra("\nPalavra para comparar (ate 15 letras): ", palavra);
    int shift = ler_inteiro("SHIFT (1 a 25): ", 1, 25);
    int n = (int)strlen(palavra);

    printf("\n%-24s %-16s %-12s %s\n", "Sequencia", "Cifrada", "Desloc.dist.", "Vazamentos");
    printf("---------------------------------------------------------------\n");

    int melhor = -1, melhor_dist = -1, melhor_vaz = 1000;
    for (int tipo = PA; tipo <= PRIMOS; tipo++) {
        /* parametros padrao: PA a1=1,r=2 ; PG a1=1,q=2 */
        gerar_sequencia(tipo, n, 1, 2, seq);
        processar(palavra, shift, seq, +1, cifrada);

        int usados[26] = {0}, distintos = 0;
        for (int i = 0; i < n; i++) {
            int d = mod26(shift + seq[i] % 26);
            if (!usados[d]) { usados[d] = 1; distintos++; }
        }

        int vazamentos = 0;
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                if (palavra[i] == palavra[j] && cifrada[i] == cifrada[j])
                    vazamentos++;

        printf("%-24s %-16s %-12d %d\n", nome_sequencia(tipo), cifrada, distintos, vazamentos);
        registrar_log("COMPARAR | palavra=%s | shift=%d | %s -> %s | distintos=%d | vazamentos=%d",
                      palavra, shift, nome_sequencia(tipo), cifrada, distintos, vazamentos);

        if (distintos > melhor_dist || (distintos == melhor_dist && vazamentos < melhor_vaz)) {
            melhor = tipo;
            melhor_dist = distintos;
            melhor_vaz = vazamentos;
        }
    }

    printf("\nMelhor resultado nesta palavra: %s\n", nome_sequencia(melhor));
    printf("(PA com a1=1 e r=2; PG com a1=1 e q=2. Altere os parametros no codigo para testar outros.)\n");
}

int main(void) {
    int opcao;
    registrar_log("=== Programa iniciado ===");

    do {
        printf("\n===== CRIPTOGRAFIA COM SEQUENCIAS =====\n");
        printf("1 - Criptografar palavra\n");
        printf("2 - Descriptografar palavra\n");
        printf("3 - Comparar sequencias (analise)\n");
        printf("0 - Sair\n");
        opcao = ler_inteiro("Opcao: ", 0, 3);

        switch (opcao) {
            case 1: opcao_criptografar();   break;
            case 2: opcao_descriptografar(); break;
            case 3: opcao_comparar();       break;
            default: break;
        }
    } while (opcao != 0);

    registrar_log("=== Programa encerrado ===");
    printf("Ate logo!\n");
    return 0;
}
