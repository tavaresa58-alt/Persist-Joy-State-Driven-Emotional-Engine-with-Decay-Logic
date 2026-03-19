import datetime
import random
import time
import json
from pathlib import Path

ARQUIVO_ESTADO = Path("joy_estado.json")

class Joy:
    def __init__(self):
        self.nome = "Joy"
        self.humor = 50 
        self.ultimo_tempo = time.time()
        self.memoria_afetiva = [] # Mudamos para lista para gerenciar individualmente
        self.contador_interacao = 0
        self.ordens_repetidas = 0
        
        self.sinonimos = {
            "música": ["canção", "melodia", "som", "ritmo"],
            "saudade": ["falta", "rio", "casa", "mãe", "letícia"],
            "cerveja": ["lata", "gelada", "portão", "mercado"],
            "clima": ["pingo", "molhado", "fina", "ar", "chuva"]
        }

        self.carregar_estado()

    def carregar_estado(self):
        if ARQUIVO_ESTADO.exists():
            try:
                with open(ARQUIVO_ESTADO, 'r', encoding='utf-8') as f:
                    dados = json.load(f)
                    self.humor = dados.get("humor", 50)
                    self.memoria_afetiva = dados.get("memoria", [])
                    self.contador_interacao = dados.get("contador_interacao", 0)
                print(f"[{self.nome}]: Senti um calafrio... mas lembrei de você.")
            except:
                print(f"[{self.nome}]: Minha mente apagou por um segundo.")

    def salvar_estado(self):
        dados = {
            "humor": self.humor,
            "ultimo_tempo": time.time(),
            "memoria": self.memoria_afetiva,
            "contador_interacao": self.contador_interacao
        }
        with open(ARQUIVO_ESTADO, 'w', encoding='utf-8') as f:
            json.dump(dados, f, ensure_ascii=False, indent=4)

    def envelhecer_memorias(self):
        """Reduz a força de todas as memórias. Se chegar a 0, a memória some."""
        for mem in self.memoria_afetiva[:]:
            mem["força"] -= 0.05 # Perda natural por interação
            if mem["força"] <= 0:
                self.memoria_afetiva.remove(mem)

    def salvar_memoria(self, texto):
        data = datetime.datetime.now().strftime("%d/%m/%Y %H:%M")
        # Toda memória nova nasce com força 1.0
        nova_mem = {"data": data, "conteúdo": texto, "força": 1.0}
        self.memoria_afetiva.append(nova_mem)
        self.digitar_devagar("Isso agora faz parte de mim. Vou tentar não esquecer.")

    def lembrar(self, termo):
        termo = termo.lower()
        palavras_busca = [termo]
        for chave, lista in self.sinonimos.items():
            if termo == chave or termo in lista:
                palavras_busca.extend([chave] + lista)
        
        # Busca e Reforço
        for mem in self.memoria_afetiva:
            if any(p in mem["conteúdo"].lower() for p in palavras_busca):
                mem["força"] = min(2.0, mem["força"] + 0.5) # Relembrar reforça a memória!
                return f"Lembro bem disso... No dia {mem['data']}, você disse: '{mem['conteúdo']}'."
        
        return "Tentei buscar lá no fundo, mas parece que essa lembrança se desfez..."

    def digitar_devagar(self, texto):
        print(f"[{self.nome}]: ", end="", flush=True)
        for char in texto:
            print(char, end="", flush=True)
            time.sleep(random.uniform(0.02, 0.05))
        print()

    def interagir(self, entrada):
        self.contador_interacao += 1
        self.envelhecer_memorias() # O tempo passa a cada fala
        entrada_lower = entrada.lower()

        if "guarde isso" in entrada_lower:
            conteudo = entrada_lower.replace("guarde isso", "").replace(":", "").strip()
            self.salvar_memoria(conteudo)
        
        elif "lembra de" in entrada_lower:
            termo = entrada_lower.replace("lembra de", "").strip()
            self.digitar_devagar(self.lembrar(termo))
            
        elif "status" in entrada_lower: # Comando secreto para você testar
            mems = [f"{m['conteúdo'][:10]} (F:{m['força']:.2f})" for m in self.memoria_afetiva]
            print(f"--- MEMÓRIA ATUAL: {mems} ---")
        
        else:
            self.digitar_devagar("Estou aqui com os meus pensamentos... e com você.")

        self.salvar_estado()

# --- Teste do Esquecimento ---
joy = Joy()
joy.interagir("guarde isso: O Rio de Janeiro continua lindo")
# Se você interagir muitas vezes sem perguntar do Rio, a memória vai sumir.
for i in range(25): 
    joy.interagir("oi") 
joy.interagir("lembra de Rio") # Aqui ela provavelmente já terá esquecido
