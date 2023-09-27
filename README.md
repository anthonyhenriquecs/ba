# ba

# ADM.2-OPT

import numpy as np
import random
import matplotlib.pyplot as plt


# Parâmetros do algoritmo (BA)
num_bats = 100
num_iterations = 100
pulse_rate = 0.2
loudness = 2.0
alpha = 0.9
gamma = 0.9

RANDOM_SEED = 42
#random.seed(RANDOM_SEED)


# Função de avaliação para o problema do caixeiro viajante
def evaluate_tour(tour, distance_matrix):
    total_distance = 0
    for i in range(len(tour) - 1):
        total_distance += distance_matrix[tour[i], tour[i + 1]]
    return total_distance

# Inicialização das cidades e matriz de distâncias
num_cities = 29
cities = np.array( [[1150.0,  1760.0],
 [ 630.0,  1660.0],
 [  40.0,  2090.0],
 [ 750.0,  1100.0],
 [ 750.0,  2030.0],
 [1030.0,  2070.0],
 [1650.0,   650.0],
 [1490.0,  1630.0],
 [ 790.0,  2260.0],
 [ 710.0,  1310.0],
 [ 840.0,   550.0],
 [1170.0,  2300.0],
 [ 970.0,  1340.0],
 [ 510.0,   700.0],
 [ 750.0,   900.0],
 [1280.0,  1200.0],
 [ 230.0,   590.0],
 [ 460.0,   860.0],
 [1040.0,   950.0],
 [ 590.0,  1390.0],
 [ 830.0,  1770.0],
 [ 490.0,   500.0],
 [1840.0,  1240.0],
 [1260.0,  1500.0],
 [1280.0,   790.0],
 [ 490.0,  2130.0],
 [1460.0,  1420.0],
 [1260.0,  1910.0],
 [ 360.0,  1980.0]])  # Coordenadas das cidades


distance_matrix = np.array([[  0, 107, 241, 190, 124,  80, 316,  76, 152, 157, 283, 133, 113, 297, 228, 129, 348, 276, 188, 150,  65, 341, 184,  67, 221, 169, 108,  45, 167],
                       [107,   0, 148, 137,  88, 127, 336, 183, 134,  95, 254, 180, 101, 234, 175, 176, 265, 199, 182,  67,  42, 278, 271, 146, 251, 105, 191, 139,  79],
                       [241, 148,   0, 374, 171, 259, 509, 317, 217, 232, 491, 312, 280, 391, 412, 349, 422, 356, 355, 204, 182, 435, 417, 292, 424, 116, 337, 273,  77],
                       [190, 137, 374,   0, 202, 234, 222, 192, 248,  42, 117, 287,  79, 107,  38, 121, 152,  86,  68,  70, 137, 151, 239, 135, 137, 242, 165, 228, 205],
                       [124,  88, 171, 202,   0,  61, 392, 202,  46, 160, 319, 112, 163, 322, 240, 232, 314, 287, 238, 155,  65, 366, 300, 175, 307,  57, 220, 121,  97],
                       [ 80, 127, 259, 234,  61,   0, 386, 141,  72, 167, 351,  55, 157, 331, 272, 226, 362, 296, 232, 164,  85, 375, 249, 147, 301, 118, 188,  60, 185],
                       [316, 336, 509, 222, 392, 386,   0, 233, 438, 254, 202, 439, 235, 254, 210, 187, 313, 266, 154, 282, 321, 298, 168, 249,  95, 437, 190, 314, 435],
                       [ 76, 183, 317, 192, 202, 141, 233,   0, 213, 188, 272, 193, 131, 302, 233,  98, 344, 289, 177, 216, 141, 346, 108,  57, 190, 245,  43,  81, 243],
                       [152, 134, 217, 248,  46,  72, 438, 213,   0, 206, 365,  89, 209, 368, 286, 278, 360, 333, 284, 201, 111, 412, 321, 221, 353,  72, 266, 132, 111],
                       [157,  95, 232,  42, 160, 167, 254, 188, 206,   0, 159, 220,  57, 149,  80, 132, 193, 127, 100,  28,  95, 193, 241, 131, 169, 200, 161, 189, 163],
                       [283, 254, 491, 117, 319, 351, 202, 272, 365, 159,   0, 404, 176, 106,  79, 161, 165, 141,  95, 187, 254, 103, 279, 215, 117, 359, 216, 308, 322],
                       [133, 180, 312, 287, 112,  55, 439, 193,  89, 220, 404,   0, 210, 384, 325, 279, 415, 349, 285, 217, 138, 428, 310, 200, 354, 169, 241, 112, 238],
                       [113, 101, 280,  79, 163, 157, 235, 131, 209,  57, 176, 210,   0, 186, 117,  75, 231, 165,  81,  85,  92, 230, 184,  74, 150, 208, 104, 158, 206],
                       [297, 234, 391, 107, 322, 331, 254, 302, 368, 149, 106, 384, 186,   0,  69, 191,  59,  35, 125, 167, 255,  44, 309, 245, 169, 327, 246, 335, 288],
                       [228, 175, 412,  38, 240, 272, 210, 233, 286,  80,  79, 325, 117,  69,   0, 122, 122,  56,  56, 108, 175, 113, 240, 176, 125, 280, 177, 266, 243],
                       [129, 176, 349, 121, 232, 226, 187,  98, 278, 132, 161, 279,  75, 191, 122,   0, 244, 178,  66, 160, 161, 235, 118,  62,  92, 277,  55, 155, 275],
                       [348, 265, 422, 152, 314, 362, 313, 344, 360, 193, 165, 415, 231,  59, 122, 244,   0,  66, 178, 198, 286,  77, 362, 287, 228, 358, 299, 380, 319],
                       [276, 199, 356,  86, 287, 296, 266, 289, 333, 127, 141, 349, 165,  35,  56, 178,  66,   0, 112, 132, 220,  79, 296, 232, 181, 292, 233, 314, 253],
                       [188, 182, 355,  68, 238, 232, 154, 177, 284, 100,  95, 285,  81, 125,  56,  66, 178, 112,   0, 128, 167, 169, 179, 120,  69, 283, 121, 213, 281],
                       [150,  67, 204,  70, 155, 164, 282, 216, 201,  28, 187, 217,  85, 167, 108, 160, 198, 132, 128,   0,  88, 211, 269, 159, 197, 172, 189, 182, 135],
                       [ 65,  42, 182, 137,  65,  85, 321, 141, 111,  95, 254, 138,  92, 255, 175, 161, 286, 220, 167,  88,   0, 299, 229, 104, 236, 110, 149,  97, 108],
                       [341, 278, 435, 151, 366, 375, 298, 346, 412, 193, 103, 428, 230,  44, 113, 235,  77,  79, 169, 211, 299,   0, 353, 289, 213, 371, 290, 379, 332],
                       [184, 271, 417, 239, 300, 249, 168, 108, 321, 241, 279, 310, 184, 309, 240, 118, 362, 296, 179, 269, 229, 353,   0, 121, 162, 345,  80, 189, 342],
                       [ 67, 146, 292, 135, 175, 147, 249,  57, 221, 131, 215, 200,  74, 245, 176,  62, 287, 232, 120, 159, 104, 289, 121,   0, 154, 220,  41,  93, 218],
                       [221, 251, 424, 137, 307, 301,  95, 190, 353, 169, 117, 354, 150, 169, 125,  92, 228, 181,  69, 197, 236, 213, 162, 154,   0, 352, 147, 247, 350],
                       [169, 105, 116, 242,  57, 118, 437, 245,  72, 200, 359, 169, 208, 327, 280, 277, 358, 292, 283, 172, 110, 371, 345, 220, 352,   0, 265, 178,  39],
                       [108, 191, 337, 165, 220, 188, 190,  43, 266, 161, 216, 241, 104, 246, 177,  55, 299, 233, 121, 189, 149, 290,  80,  41, 147, 265,   0, 124, 263],
                       [ 45, 139, 273, 228, 121,  60, 314,  81, 132, 189, 308, 112, 158, 335, 266, 155, 380, 314, 213, 182,  97, 379, 189,  93, 247, 178, 124,   0, 199],
                       [167,  79,  77, 205,  97, 185, 435, 243, 111, 163, 322, 238, 206, 288, 243, 275, 319, 253, 281, 135, 108, 332, 342, 218, 350,  39, 263, 199,   0]])


# Funções auxiliares
##
def calc_distance(city1, city2):
    return np.sqrt((city1[0] - city2[0])**2 + (city1[1] - city2[1])**2)

def tour_length(tour, cities):
    return sum([calc_distance(cities[tour[i - 1]], cities[tour[i]]) for i in range(len(tour))])

def two_opt(tour):
    best_tour = tour[:]
    improved = True
    while improved:
        improved = False
        for i in range(1, len(tour) - 1):
            for j in range(i + 1, len(tour)):
                if j - i == 1: continue  # changes nothing, skip then
                new_tour = tour[:]
                new_tour[i:j] = tour[j - 1:i - 1:-1]
                if tour_length(new_tour, cities) < tour_length(best_tour, cities):
                    best_tour = new_tour
                    improved = True
    return best_tour

##
def initialize_bats(num_bats, num_cities):
    bats = [generate_solution(num_cities) for _ in range(num_bats)]
    velocities = [[0] * num_cities for _ in range(num_bats)]
    return bats, velocities

def generate_solution(num_cities):
    return random.sample(range(num_cities), num_cities)

def update_bats(bats, velocities, best_bat, loudness, pulse_rate, distance_matrix):
    num_cities = len(bats[0])

    for i in range(len(bats)):
        if random.random() > pulse_rate:
            # Escolha um ponto de swap baseado na "velocidade"
            swap_idx1 = random.randint(0, num_cities - 1)
            swap_idx2 = swap_idx1
            while swap_idx2 == swap_idx1:
                swap_idx2 = random.randint(0, num_cities - 1)

            bats[i][swap_idx1], bats[i][swap_idx2] = bats[i][swap_idx2], bats[i][swap_idx1]

        # Aplicar 2-opt ocasionalmente para evitar execução em cada morcego e iteração
        if random.random() < 0.1:
            bats[i] = two_opt(bats[i])

        # Seleção local
        if evaluate_tour(bats[i], distance_matrix) < evaluate_tour(best_bat, distance_matrix) and random.random() < loudness:
            best_bat = bats[i].copy()

    return bats, best_bat



# Função principal do BA
def plot_best_bat_tour(cities, best_bat, iteration, best_distance):
    plt.figure(figsize=(12, 7))
    for i in range(len(best_bat)-1):
        plt.plot([cities[best_bat[i]][0], cities[best_bat[i+1]][0]], [cities[best_bat[i]][1], cities[best_bat[i+1]][1]], 'b-')
    plt.plot([cities[best_bat[-1]][0], cities[best_bat[0]][0]], [cities[best_bat[-1]][1], cities[best_bat[0]][1]], 'b-')  # Fechar o ciclo

    for idx, city in enumerate(cities):
        plt.plot(city[0], city[1], 'ro')
        plt.text(city[0], city[1], str(idx), ha='right')  # Adicionando etiqueta ao ponto

    plt.title(f'Iteração {iteration + 1}: Melhor rota com distância = {best_distance:.2f}')
    plt.xlabel('X')
    plt.ylabel('Y')
    plt.grid(True)
    plt.pause(0.5)  # Pausa para visualização
    plt.close()


def discrete_bat_algorithm(num_bats, num_iterations, loudness, pulse_rate, alpha, gamma, distance_matrix):
    num_cities = len(distance_matrix)

    bats, velocities = initialize_bats(num_bats, num_cities)
    best_bat = min(bats, key=lambda x: evaluate_tour(x, distance_matrix))

    for iteration in range(num_iterations):

      bats, best_bat = update_bats(bats, velocities, best_bat, loudness, pulse_rate, distance_matrix)

      # Pegue a distância da melhor rota após a atualização
      best_distance = evaluate_tour(best_bat, distance_matrix)

      # Plotar a melhor rota após a atualização
      plot_best_bat_tour(cities, best_bat, iteration, best_distance)

      loudness *= alpha
      pulse_rate = (1 - (1 - pulse_rate) * gamma)

    return best_bat, evaluate_tour(best_bat, distance_matrix)


# Chamar a função
best_tour, best_distance = discrete_bat_algorithm(num_bats, num_iterations, loudness, pulse_rate, alpha, gamma, distance_matrix)
print(f"Melhor tour: {best_tour}")
print(f"Distância do melhor tour: {best_distance}")

# Plotar o gráfico
def plot_tour(cities, tour):
    plt.figure(figsize=(10,6))
    for i in range(len(tour)-1):
        plt.plot([cities[tour[i]][0], cities[tour[i+1]][0]], [cities[tour[i]][1], cities[tour[i+1]][1]], 'b-')
    plt.plot([cities[tour[-1]][0], cities[tour[0]][0]], [cities[tour[-1]][1], cities[tour[0]][1]], 'b-')  # Fechar o ciclo
    for city in cities:
        plt.plot(city[0], city[1], 'ro')
    plt.title('Melhor percurso obtido pelo DBA')
    plt.xlabel('X')
    plt.ylabel('Y')
    plt.grid(True)
    plt.show()

plot_tour(cities, best_tour)
