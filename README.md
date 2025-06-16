opcao = 0

usuarios = []
viagens = []
reservas = []

def cadastrar_usuario():
    nome = input("Digite seu nome: ")
    email = input("Digite seu email: ")
    senha = input("Digite sua senha: ")
    confSenha = input("Digite sua senha novamente: ")

    emailExiste = False
    for usu in usuarios:
        if email == usu["email"]:
            print('Email já existe!')
            emailExiste = True
            break
    if emailExiste:
        return

    if '@' in email and '.' in email:
        if senha == confSenha:
            if 7 <= len(senha) <= 20:
                print('Usuário cadastrado com sucesso')
                usuario = {
                    'nome': nome,
                    'email': email,
                    'senha': senha,
                }
                usuarios.append(usuario)
            else:
                print('Senha precisa ter entre 7 e 20 caracteres')
        else:
            print("As senhas não conferem")
    else:
        print('Email inválido!')

def listar_usuarios():
    for usu in usuarios:
        print(f"Nome: {usu['nome']}")
        print(f"Email: {usu['email']}")
        print('')

def login():
    email = input('Digite seu email: ')
    senha = input('Digite sua senha: ')

    logado = False
    for usu in usuarios:
        if usu['email'] == email and usu['senha'] == senha:
            print(f"Login realizado com sucesso! Bem-vindo, {usu['nome']}.")
            logado = True
            return usu
    if not logado:
        print("Email ou senha incorretos.")
        return None

def cadastrar_viagem(usuario_logado):
    origem = input('Digite a origem: ')
    destino = input('Digite o destino: ')
    vagas = input('Digite o número de vagas: ')
    data = input('Digite a data (formato DD/MM/AA): ')
    hora = input('Digite a hora (formato HH:MM): ')
    valor = input('Digite o valor por vaga: ')
    viagem = {
        'motorista': usuario_logado['nome'],
        'motorista_email': usuario_logado['email'],
        'origem': origem,
        'destino': destino,
        'vagas': int(vagas),
        'data': data,
        'hora': hora,
        'valor': float(valor)
    }
    viagens.append(viagem)
    print("Viagem cadastrada com sucesso!")

def listar_carona_disponiveis():
    if len(viagens) == 0:
        print("Nenhuma carona disponível.")
        return
    for via in viagens:
        if via['vagas'] > 0:
            print("Motorista:", via['motorista'])
            print("Email do motorista:", via['motorista_email'])
            print("Origem:", via['origem'])
            print("Destino:", via['destino'])
            print("Data:", via['data'])
            print("Hora:", via['hora'])
            print("Vagas disponíveis:", via['vagas'])
            print("Valor por vaga: R$", via['valor'])
            print('-' * 40)

def buscar_carona_por_origem_destino():
    origem = input("Digite a origem: ")
    destino = input("Digite o destino: ")
    encontrou = False
    for via in viagens:
        if via['origem'].lower() == origem.lower() and via['destino'].lower() == destino.lower() and via['vagas'] > 0:
            encontrou = True
            print("Motorista:", via['motorista'])
            print("Email do motorista:", via['motorista_email'])
            print("Origem:", via['origem'])
            print("Destino:", via['destino'])
            print("Data:", via['data'])
            print("Hora:", via['hora'])
            print("Vagas disponíveis:", via['vagas'])
            print("Valor por vaga: R$", via['valor'])
            print('-' * 40)
    if not encontrou:
        print("Nenhuma carona encontrada com esses critérios.")

def reservar_vaga(usuario_logado):
    email_motorista = input("Digite o email do motorista da carona que deseja reservar: ")
    data_carona = input("Digite a data da carona (formato DD/MM/AA): ")
    quantidade = int(input("Quantas vagas deseja reservar? "))

    viagem_encontrada = None
    for via in viagens:
        if via['motorista_email'] == email_motorista and via['data'] == data_carona:
            viagem_encontrada = via
            break

    if viagem_encontrada is None:
        print("Carona não encontrada com esses dados.")
        return

    if viagem_encontrada['vagas'] >= quantidade and quantidade > 0:
        viagem_encontrada['vagas'] -= quantidade
        reservas.append({
            'usuario': usuario_logado['nome'],
            'motorista_email': email_motorista,
            'data': data_carona,
            'vagas_reservadas': quantidade
        })
        print(f"Reserva realizada com sucesso! Vagas restantes: {viagem_encontrada['vagas']}")
        print("O pagamento deve ser feito diretamente ao motorista no momento da entrega.")
        print("\n--- Detalhes da sua reserva ---")
        print("Motorista:", viagem_encontrada['motorista'])
        print("Email do motorista:", viagem_encontrada['motorista_email'])
        print("Origem:", viagem_encontrada['origem'])
        print("Destino:", viagem_encontrada['destino'])
        print("Data:", viagem_encontrada['data'])
        print("Hora:", viagem_encontrada['hora'])
        print("Valor por vaga: R$", viagem_encontrada['valor'])
        print("Vagas reservadas:", quantidade)
        print("-----------------------------------\n")
    else:
        print("Não há vagas suficientes ou quantidade inválida.")

def cancelar_reserva(usuario_logado):
    email_motorista = input("Digite o email do motorista da carona que deseja cancelar reserva: ")
    data_carona = input("Digite a data da carona (formato DD/MM/AA): ")

    reserva_encontrada = None
    for reserva in reservas:
        if reserva['usuario'] == usuario_logado['nome'] and reserva['motorista_email'] == email_motorista and reserva['data'] == data_carona:
            reserva_encontrada = reserva
            break

    if reserva_encontrada is None:
        print("Você não tem reserva para essa carona.")
        return

    for via in viagens:
        if via['motorista_email'] == email_motorista and via['data'] == data_carona:
            via['vagas'] += reserva_encontrada['vagas_reservadas']
            print(f"Cancelamento realizado com sucesso! {reserva_encontrada['vagas_reservadas']} vaga(s) devolvida(s).")
            print(f"Vagas disponíveis agora: {via['vagas']}")
            reservas.remove(reserva_encontrada)
            break

def remover_carona(usuario_logado):
    data_carona = input("Digite a data da carona que deseja remover (formato DD/MM/AA): ")
    carona_encontrada = None
    for via in viagens:
        if via['motorista_email'] == usuario_logado['email'] and via['data'] == data_carona:
            carona_encontrada = via
            break
    if carona_encontrada is None:
        print("Você não tem uma carona cadastrada nessa data.")
        return
    viagens.remove(carona_encontrada)
    print("Carona removida com sucesso!")

def mostrar_detalhes_carona():
    email_motorista = input("Digite o email do motorista da carona: ")
    data_carona = input("Digite a data da carona (formato DD/MM/AA): ")
    carona_encontrada = None
    for via in viagens:
        if via['motorista_email'] == email_motorista and via['data'] == data_carona:
            carona_encontrada = via
            break
    if carona_encontrada is None:
        print("Carona não encontrada.")
        return
    print("Detalhes da Carona:")
    print("Motorista:", carona_encontrada['motorista'])
    print("Email do motorista:", carona_encontrada['motorista_email'])
    print("Origem:", carona_encontrada['origem'])
    print("Destino:", carona_encontrada['destino'])
    print("Data:", carona_encontrada['data'])
    print("Hora:", carona_encontrada['hora'])
    print("Valor por vaga: R$", carona_encontrada['valor'])
    print("Vagas restantes:", carona_encontrada['vagas'])

def mostrar_carona_usuario(usuario_logado):
    tem_carona = False
    for via in viagens:
        if via['motorista_email'] == usuario_logado['email']:
            tem_carona = True
            print("Origem:", via['origem'])
            print("Destino:", via['destino'])
            print("Data:", via['data'])
            print("Hora:", via['hora'])
            print("Valor por vaga: R$", via['valor'])
            print("Vagas restantes:", via['vagas'])
            print('-' * 30)
    if not tem_carona:
        print("Você não tem caronas cadastradas.")

def logout():
    print("Logout realizado com sucesso.")

usuario_logado = None

while opcao != 4:
    print('-' * 50)
    print('''
          Escolha uma opção:
          1- Cadastrar
          2- Login
          3- Lista usuário
          4- Sair
        ''')
    print('-' * 50)

    try:
        opcao = int(input("Escolha uma opção: "))
    except:
        print("Digite um número válido.")
        continue

    if opcao == 4:
        print("Saiu")
        break

    elif opcao == 1:
        cadastrar_usuario()

    elif opcao == 3:
        listar_usuarios()

    elif opcao == 2:
        usuario_logado = login()
        if usuario_logado is not None:
            opcao_menu = 0
            while opcao_menu != 9:
                print('''
                    Menu Usuário:
                    [1] Cadastrar Carona
                    [2] Listar todas as caronas disponíveis
                    [3] Buscar carona por origem e destino
                    [4] Reservar vaga em carona
                    [5] Cancelar reserva
                    [6] Remover carona
                    [7] Mostrar detalhes de uma carona
                    [8] Mostrar caronas cadastradas (motorista)
                    [9] Logout
                ''')
                try:
                    opcao_menu = int(input('Escolha uma opção: '))
                except:
                    print("Digite um número válido.")
                    continue

                if opcao_menu == 1:
                    cadastrar_viagem(usuario_logado)

                elif opcao_menu == 2:
                    listar_carona_disponiveis()

                elif opcao_menu == 3:
                    buscar_carona_por_origem_destino()

                elif opcao_menu == 4:
                    reservar_vaga(usuario_logado)

                elif opcao_menu == 5:
                    cancelar_reserva(usuario_logado)

                elif opcao_menu == 6:
                    remover_carona(usuario_logado)

                elif opcao_menu == 7:
                    mostrar_detalhes_carona()

                elif opcao_menu == 8:
                    mostrar_carona_usuario(usuario_logado)

                elif opcao_menu == 9:
                    logout()
                    usuario_logado = None
                    break

                else:
                    print("Opção inválida.")

