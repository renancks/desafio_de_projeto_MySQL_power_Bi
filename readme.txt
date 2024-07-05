### README - Projeto de Transformação de Dados para Power BI

#### Descrição do Projeto

Este projeto visa transformar uma base de dados MySQL com informações de empregados, dependentes, departamentos, locais de departamento, projetos e horas trabalhadas, antes de transferi-los para o Power BI para análise e visualização. As transformações garantem a integridade dos dados, ajustam os tipos de dados conforme necessário, tratam valores nulos, e preparam os dados para modelagem e visualização eficiente.

#### Passo a Passo

##### 1. Verificação e Preparação Inicial

1. **Verificação de Cabeçalhos e Tipos de Dados:**
   - Verifique os nomes das colunas e os tipos de dados em todas as tabelas.
   ```sql
   DESCRIBE employee;
   DESCRIBE dependent;
   DESCRIBE departament;
   DESCRIBE dept_locations;
   DESCRIBE project;
   DESCRIBE works_on;
   ```

2. **Modificação dos Valores Monetários para o Tipo Double:**
   - Alterar a coluna `Salary` na tabela `employee` para o tipo `DOUBLE`.
   ```sql
   ALTER TABLE employee MODIFY Salary DOUBLE(10, 2);
   ```

##### 2. Tratamento de Valores Nulos

1. **Verificação e Remoção de Valores Nulos:**
   - Identificar valores nulos em todas as tabelas.
   ```sql
   SELECT * FROM employee WHERE Super_ssn IS NULL;
   SELECT * FROM departament WHERE Mgr_ssn IS NULL;
   ```

2. **Preenchimento de Valores Nulos:**
   - Atualizar a coluna `Super_ssn` na tabela `employee` onde `Super_ssn` é nulo.
   ```sql
   UPDATE employee SET Super_ssn = 123456789 WHERE Super_ssn IS NULL;
   ```

##### 3. Verificação de Consistência

1. **Verificação de Existência de Colaboradores Sem Gerente:**
   ```sql
   SELECT * FROM employee WHERE Super_ssn IS NULL;
   ```

2. **Verificação de Departamentos Sem Gerente:**
   ```sql
   SELECT * FROM departament WHERE Mgr_ssn IS NULL;
   ```

##### 4. Transformações Avançadas

1. **Verificação do Número de Horas dos Projetos:**
   ```sql
   SELECT * FROM works_on WHERE Hours IS NULL OR Hours < 0;
   ```

2. **Separação de Colunas Complexas:**
   - Separar colunas complexas em partes mais simples, se necessário.
   ```sql
   SELECT CONCAT(Fname, ' ', Lname) AS Full_Name, Address FROM employee;
   ```

3. **Junção de Tabelas:**
   - Mesclar a tabela `employee` com `departament` para criar uma tabela com o nome dos departamentos associados aos colaboradores.
   ```sql
   SELECT e.*, d.Dname AS Department_Name
   FROM employee e
   JOIN departament d ON e.Dno = d.Dnumber;
   ```

4. **Eliminação de Colunas Desnecessárias:**
   - Eliminar colunas que não serão usadas no relatório.
   ```sql
   SELECT e.Ssn, CONCAT(e.Fname, ' ', e.Lname) AS Full_Name, e.Address, e.Salary, d.Dname AS Department_Name
   FROM employee e
   JOIN departament d ON e.Dno = d.Dnumber;
   ```

5. **Mesclagem de Colunas:**
   - Mesclar os nomes de departamentos e localização.
   ```sql
   SELECT CONCAT(d.Dname, ' - ', l.Dlocation) AS Department_Location
   FROM departament d
   JOIN dept_locations l ON d.Dnumber = l.Dnumber;
   ```

6. **Agrupamento de Dados:**
   - Agrupar os dados para saber quantos colaboradores existem por gerente.
   ```sql
   SELECT Super_ssn, COUNT(*) AS Number_of_Employees
   FROM employee
   GROUP BY Super_ssn;
   ```

##### 5. Consulta Final para Junção de Colaboradores e Gerentes

```sql
SELECT e1.Ssn, CONCAT(e1.Fname, ' ', e1.Lname) AS Employee_Name, e2.Ssn AS Manager_Ssn, CONCAT(e2.Fname, ' ', e2.Lname) AS Manager_Name
FROM employee e1
LEFT JOIN employee e2 ON e1.Super_ssn = e2.Ssn;
```

#### Conclusão

Este README documenta as etapas realizadas para transformar e preparar os dados de uma base de dados MySQL para análise e visualização no Power BI. As transformações garantem a integridade dos dados, ajustam tipos de dados conforme necessário, tratam valores nulos e preparam os dados para modelagem eficiente.