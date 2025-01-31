# Desafio Accenture SQL L1

## 0 ⦁	Sendo que somente um Adm pode criar novas questões. (EXTRA)
```sql
CREATE TRIGGER check_admin_before_insert
BEFORE INSERT ON QUESTOES
FOR EACH ROW
BEGIN
    DECLARE msg VARCHAR(255);
    DECLARE tipo_usuario VARCHAR(50);

    -- Assumindo que você tem uma função que retorna o tipo de usuário baseado no ID
    SET tipo_usuario = (SELECT TIPO FROM USUARIO WHERE idUSUARIO = NEW.USUARIO_idUSUARIO);

    IF tipo_usuario != 'Administrador' THEN
        SET msg = 'Operação INSERT permitida apenas para Administradores';
        SIGNAL SQLSTATE '45000' SET MESSAGE_TEXT = msg;
    END IF;
END;
```

## 1 ⦁	Escreva as instruções sql para popular as tabelas usuario  (um adm e um jogaodor) e questoes (somente uma questão);
```sql
-- Administrador
INSERT INTO USUARIO (idUSUARIO, NICK, NOME_USU, SENHA, TIPO) 
VALUES (1, 'admin_nick', 'admin_nome', 'admin_senha', 'Administrador');
-- Jogador
INSERT INTO USUARIO (idUSUARIO, NICK, NOME_USU, SENHA, TIPO) 
VALUES (2, 'jogador_nick', 'jogador_nome', 'jogador_senha', 'Jogador');
-- Questão
INSERT INTO QUESTOES (idQuestoes, USUARIO_idUSUARIO, DESCRICAO) 
VALUES (1, 1, 'Qual é a capital do Rio de Janeiro?');
```

## 2. Instrução SQL para alterar o conteúdo da descrição da primeira questão inserida:
```sql
UPDATE QUESTOES 
SET DESCRICAO = 'Qual é a capital de São Paulo?'
WHERE idQuestoes = 1;
```

## 3. Instrução SQL para selecionar as maiores pontuações de cada jogador por data, ordenando do maior para o menor (apresentar: Nick; Data_Jogo; Pontuacao_Geral):
```sql
SELECT U.NICK, J.DATA_jogo, J.TOTAL_PONTOS 
FROM JOGOS J
JOIN USUARIO U ON J.USUARIO_idUSUARIO = U.idUSUARIO
ORDER BY J.TOTAL_PONTOS DESC, J.DATA_jogo DESC;
```

## 4. Instrução SQL para selecionar a quantidade de erros e acertos por cada alternativa (apresentar: Questão (descricao); Alternativa (descricao); Total_Erros; Total_Acertos):
```sql
SELECT Q.DESCRICAO AS Questao, A.DESCRICAO AS Alternativa,
       SUM(CASE WHEN A.CORRETA = '0' THEN 1 ELSE 0 END) AS Total_Erros,
       SUM(CASE WHEN A.CORRETA = '1' THEN 1 ELSE 0 END) AS Total_Acertos
FROM RESPOSTAS R
JOIN ALTERNATIVAS A ON R.Questoes_idQuestoes = A.IDQUESTOES
JOIN QUESTOES Q ON A.IDQUESTOES = Q.idQuestoes
GROUP BY Q.DESCRICAO, A.DESCRICAO;
```

## 5. Instrução SQL para selecionar a média de pontuação de cada jogador (apresentar: Nick; Media):
```sql
SELECT U.NICK, AVG(J.TOTAL_PONTOS) AS Media
FROM JOGOS J
JOIN USUARIO U ON J.USUARIO_idUSUARIO = U.idUSUARIO
GROUP BY U.NICK;
```

## 6. Instrução SQL para selecionar o(s) jogador(es) cadastrado(s) sem nenhum jogo efetuado (apresentar: Nome_jogador):
```sql
SELECT U.NOME_USU AS Nome_jogador
FROM USUARIO U
LEFT JOIN JOGOS J ON U.idUSUARIO = J.USUARIO_idUSUARIO
WHERE J.idJOGO IS NULL AND U.TIPO = 'Jogador';
```
