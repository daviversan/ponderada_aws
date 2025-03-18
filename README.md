## Introdução
Este repositório contém o conteúdo da ponderada 'Elaboração de aplicação integrada a um banco de dados'. A seguir está o código em PHP que cria a página para fazer as operações de criação e listagem dos registros da tabela criada no banco de dados. A instância EC2 e banco de dados MySQL foram criados conforme o tutorial da AWS disponível no link do card da ponderada. O vídeo demonstrativo se encontra logo abaixo do código

## Código em PHP
```

<?php include "../inc/dbinfo.inc"; ?>
<html>
<body>
<h1>Sample page</h1>
<?php
  /* Connect to MySQL and select the database. */
  $connection = mysqli_connect(DB_SERVER, DB_USERNAME, DB_PASSWORD);

  if (mysqli_connect_errno()) echo "Failed to connect to MySQL: " . mysqli_connect_error();

  $database = mysqli_select_db($connection, DB_DATABASE);

  /* Ensure that the EMPLOYEES table exists. */
  VerifyEmployeesTable($connection, DB_DATABASE);

  /* If input fields are populated, add a row to the EMPLOYEES table. */
  $employee_name = htmlentities($_POST['NAME']);
  $employee_address = htmlentities($_POST['ADDRESS']);
  $employee_salary = htmlentities($_POST['SALARY']);
  $employee_hire_date = htmlentities($_POST['HIRE_DATE']);

  if (strlen($employee_name) || strlen($employee_address) || strlen($employee_salary) || strlen($employee_hire_date)) {
    AddEmployee($connection, $employee_name, $employee_address, $employee_salary, $employee_hire_date);
  }
?>

<!-- Input form -->
<form action="<?PHP echo $_SERVER['SCRIPT_NAME'] ?>" method="POST">
  <table border="0">
    <tr>
      <td>NAME</td>
      <td>ADDRESS</td>
      <td>SALARY</td>
      <td>HIRE DATE</td>
    </tr>
    <tr>
      <td><input type="text" name="NAME" maxlength="45" size="30" /></td>
      <td><input type="text" name="ADDRESS" maxlength="90" size="60" /></td>
      <td><input type="text" name="SALARY" maxlength="10" size="15" /></td>
      <td><input type="date" name="HIRE_DATE" /></td>
      <td><input type="submit" value="Add Data" /></td>
    </tr>
  </table>
</form>

<!-- Display table data. -->
<table border="1" cellpadding="2" cellspacing="2">
  <tr>
    <td>ID</td>
    <td>NAME</td>
    <td>ADDRESS</td>
    <td>SALARY</td>
    <td>HIRE DATE</td>
  </tr>

<?php
$result = mysqli_query($connection, "SELECT * FROM EMPLOYEES");

while($query_data = mysqli_fetch_row($result)) {
  echo "<tr>";
  echo "<td>", $query_data[0], "</td>",
       "<td>", $query_data[1], "</td>",
       "<td>", $query_data[2], "</td>",
       "<td>", $query_data[3], "</td>",
       "<td>", $query_data[4], "</td>";
  echo "</tr>";
}
?>

</table>

<!-- Clean up. -->
<?php
  mysqli_free_result($result);
  mysqli_close($connection);
?>

</body>
</html>

<?php
/* Add an employee to the table. */
function AddEmployee($connection, $name, $address, $salary, $hire_date) {
   $n = mysqli_real_escape_string($connection, $name);
   $a = mysqli_real_escape_string($connection, $address);
   $s = mysqli_real_escape_string($connection, $salary);
   $h = mysqli_real_escape_string($connection, $hire_date);

   $query = "INSERT INTO EMPLOYEES (NAME, ADDRESS, SALARY, HIRE_DATE) VALUES ('$n', '$a', '$s', '$h');";

   if(!mysqli_query($connection, $query)) echo("<p>Error adding employee data.</p>");
}

/* Check whether the table exists and, if not, create it. */
function VerifyEmployeesTable($connection, $dbName) {
  if(!TableExists("EMPLOYEES", $connection, $dbName)) {
     $query = "CREATE TABLE EMPLOYEES (
         ID int(11) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
         NAME VARCHAR(45),
         ADDRESS VARCHAR(90),
         SALARY DECIMAL(10,2),
         HIRE_DATE DATE
       )";

     if(!mysqli_query($connection, $query)) echo("<p>Error creating table.</p>");
  }
}

/* Check for the existence of a table. */
function TableExists($tableName, $connection, $dbName) {
  $t = mysqli_real_escape_string($connection, $tableName);
  $d = mysqli_real_escape_string($connection, $dbName);

  $checktable = mysqli_query($connection,
      "SELECT TABLE_NAME FROM information_schema.TABLES WHERE TABLE_NAME = '$t' AND TABLE_SCHEMA = '$d'");

  if(mysqli_num_rows($checktable) > 0) return true;

  return false;
}
?>

```

## Vídeo demonstrando a aplicação rodando:
Vídeo [aqui](https://youtu.be/pK1kCCawLY8)
