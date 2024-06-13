# DMS-creation-settings
Database Migatrion Service settings for optimal migration and creation

# Criação da instancia de replicação, endpoints e tasks:
- Acesso o painel do DMS, clique em "replication instances e em "create replication instance".
- Dê um nome para a instância de replicação, selecione a classe de instância desejada, assim como a versão da engine, a opção de alta disponibilidade e o tamanho do storage. (As configurações para otimizar a instancia estarão na parte de "Configurações de otimização e conectividade").
- Em "network type", selecione a opção "IPV4" e selecione a mesma VPC que o seu banco de origem e seu banco de destino estão.
- Selecione ou crie um subnet group para replicação e, de acordo com sua preferência, marque ou não a caixa de "public accessible".
- Após a instancia de replicação ficar com o status "available", crie os endpoints.
- Para criar os endpoints vá na aba "endpoints" e clique em "create endpoint".
- Criaremos tanto o source endpoint quanto o target endpoint. Na hora da criação, marque a caixa "select rds db instance" e selecione sua instancia de origem/destino.
- Nas configurações, selecione a source engine desejada e depois clique em "provide access information manually" na opção de "access to endpoint database".
- Em "username" e "password" passe o user master do banco de origem/destino e senha. Clique em "create endpoint".

  
*Lembrando que na hora da criação dos endpoints, o "source endpoint" é o seu banco de origem e o "target endpoint" é o banco de destino.*


- Após a criação dos endpoints, crie as tasks de migração em "database migration tasks".
- Clique em "create task" e passe as informações necessárias(task identifier,replication instance, source database endpoint e target database endpoint).
*Para o resto das configurações, siga a parte de "Configurações de otimização e conectividade".*

# Configurações de otimização e conectividade parte 1
- Para rodar de forma otimizada a instância de replicação, recomendo o uso da classe "dms.c5.2xlarge", sem a opção de Multi-AZ (apenas quando necessário), e um storage de 100 GB ou mais (de acordo com sua necessidade).
- O security group da instância de replicação deverá ter em suas inbound rules a liberação para "all traffic" dos grupos de segurança do RDS de origem e do RDS de destino.
- No security group do RDS de origem e no de destino, também deverá ter o grupo de segurança da instancia de replicação liberado para "all trafic" em suas inbound rules.

# Configurações de otimização e conectividade parte 2
- Em "database migration tasks", use as seguintes configurações para otimizar as tasks(leia a documentação para entender sua necessidade https://docs.aws.amazon.com/dms/).
- Em "task settings" selecione "wizard" e deixe da seguinte forma: Target table preparation mode = truncate/LOB column settings = Full LOB mode/LOB chunk size(kb) = 64/Data validation = Turn off/Task logs = Turn on Cloudwatch logs/Log context = Turn on log context/Log component severity = deixe tudo default.
- Clique em "advanced task settings" e deixe da seguinte forma: History timeslot(minutes) = 5/Create control table in target using schema = deixe em branco/Na tabela de controle e name in targe, deixe todas as opções marcadas.
- Em "full load tuning settings" deixe da seguinte forma: Maximum number of tables to load in parallel = 16/Transaction consistency timeout(seconds) = 600/Commit rate during full load = 50000.
- Após setar essas configurações clique em "save".

  
*Lembrando que essas configurações de otimização foram baseadas na necessidade que eu tive. Com essas configurações, é possível ter uma ideia de como modificá-las de acordo com a sua necessidade.*
