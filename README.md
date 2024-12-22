# OptimalKeyboard
# Genetik algoritmayı kullanarak optimal Türkçe klavye oluşturdum. 
import random

# Klavyede bulunacak karakterler (Türk alfabesi, sayılar, boşluk ve noktalama işaretleri dahil)
CHARACTERS = (
    "abcçdefgğhıijklmnoöprsştuüvyz" +  # Türk alfabesi
    "0123456789" +                   # Sayılar
    " .,!?;:()[]{}<>@#$%^&*-_+=|~" +  # Noktalama işaretleri
    "\n \t"                          # Boşluk ve özel karakterler (Enter, Tab)
)

# Klavyenin toplam boyutu (örneğin tüm karakterler kullanılıyor)
KEYBOARD_SIZE = len(CHARACTERS)

# Parametreler
generations = 100  # Nesil sayısı
population_size = 200  # Popülasyon boyutu
mutation_rate = 0.1  # Mutasyon oranı

# Fitness fonksiyonu (örnek bir ergonomik değerlendirme):
def calculate_fitness(keyboard):
    """Ergonomiyi ölçmek için fitness fonksiyonu."""
    fitness = 0
    row_weights = [1, 2, 3, 4]  # Satır bazında ağırlıklar (üst sıralar daha ergonomik kabul edilir)
    rows = [keyboard[:10], keyboard[10:20], keyboard[20:30], keyboard[30:]]  # Klavye satırları

    for i, row in enumerate(rows):
        if i >= len(row_weights):  # Satır ağırlık sınırını aşarsa geç
            continue
        for char in row:
            # Sık kullanılan harfler ergonomiye katkı sağlar
            if char in "etaoinshrdlu":
                fitness += row_weights[i]
    return fitness

# Rastgele bir klavye düzeni oluştur
def generate_random_keyboard():
    chars = list(CHARACTERS)
    random.shuffle(chars)
    return chars

# Çaprazlama (Crossover)
def crossover(parent1, parent2):
    split = random.randint(1, KEYBOARD_SIZE - 2)
    child1 = parent1[:split] + [c for c in parent2 if c not in parent1[:split]]
    child2 = parent2[:split] + [c for c in parent1 if c not in parent2[:split]]
    return child1, child2

# Mutasyon (Mutation)
def mutate(keyboard):
    keyboard = list(keyboard)
    if random.random() < mutation_rate:
        i, j = random.sample(range(len(keyboard)), 2)
        keyboard[i], keyboard[j] = keyboard[j], keyboard[i]
    return keyboard

# Popülasyonun başlangıç ayarları
population = [generate_random_keyboard() for _ in range(population_size)]

# Genetik Algoritma
for generation in range(generations):
    # Fitness değerlerini hesapla
    population_fitness = [(calculate_fitness(individual), individual) for individual in population]
    population_fitness.sort(reverse=True, key=lambda x: x[0])

    print(f"Generation {generation + 1} Best Fitness: {population_fitness[0][0]}")

    # Seçilim (Selection): En iyi bireyler üzerinden seç
    selected = [individual for _, individual in population_fitness[:population_size // 2]]

    # Çaprazlama ve mutasyon ile yeni bireyler oluştur
    new_population = []
    while len(new_population) < population_size:
        parent1, parent2 = random.sample(selected, 2)
        child1, child2 = crossover(parent1, parent2)
        new_population.append(mutate(child1))
        if len(new_population) < population_size:
            new_population.append(mutate(child2))

    population = new_population

# En iyi klavye düzenini seç
best_keyboard = population_fitness[0][1]
print("\nOptimal Keyboard Design:")
for i in range(0, len(best_keyboard), 10):
    print(" ".join(best_keyboard[i:i+10]))
