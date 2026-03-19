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
