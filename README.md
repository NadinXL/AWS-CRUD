<div align="center">
  
# 🛠️ AWS Lambda CRUD

</div>
Este repositorio contiene un ejemplo de una API CRUD utilizando AWS Lambda, API Gateway y MySQL. Sigue los pasos a continuación para implementar y probar el proyecto.


## 🌟 Introducción
Este proyecto muestra cómo crear y desplegar un API CRUD utilizando AWS Lambda, API Gateway, y MySQL. Cada función (Crear, Leer, Actualizar, Eliminar) está gestionada a través de solicitudes HTTP (POST, GET, PUT, DELETE).

### 1. Configuración de AWS
  ### - Base de Datos: Configura una instancia MySQL en Amazon RDS
       
   - Configura las variables de conexión en el código Lambda (host, user, password, database)
     
      ![image](https://github.com/user-attachments/assets/80bc9b95-94ed-4229-ac14-ddc7df58098b)

### 2. Función Lambda: Despliega la función Lambda

   - Código de la función Lambda:
   ```
  import mysql from 'mysql';

const con = mysql.createConnection({
  host: 'mydb.cfm83ft45qd.us-east-2.rds.amazonaws.com',
  user: 'user_name',
  port: "3306",
  password: 'password',
  database: 'database_name',
});

export const handler = async (event, context) => {
  context.callbackWaitsForEmptyEventLoop = false;

  const query = (sql, values) => new Promise((resolve, reject) => {
    con.query(sql, values, (err, res) => {
      if (err) {
        return reject(err);
      }
      resolve(res);
    });
  });

  try {
    // Detectar el método HTTP
    switch (event.httpMethod) {
      case 'POST': {
        const { estado } = JSON.parse(event.body);
        const sql = "INSERT INTO tb_estado (estado) VALUES (?)";
        await query(sql, [estado]);
        return {
          statusCode: 200,
          body: JSON.stringify({ message: 'Se registró el valor.' }),
        };
      }

      case 'GET': {
        const sql = "SELECT * FROM tb_estado";
        const results = await query(sql);
        return {
          statusCode: 200,
          body: JSON.stringify(results),
        };
      }

      case 'PUT': {
        const { id_estado, estado } = JSON.parse(event.body);
        const sql = "UPDATE tb_estado SET estado = ? WHERE id_estado = ?";
        await query(sql, [estado, id_estado]);
        return {
          statusCode: 200,
          body: JSON.stringify({ message: 'Se actualizó el valor.' }),
        };
      }

      case 'DELETE': {
        const id_estado = event.queryStringParameters?.id_estado;
        if (!id_estado) {
          return {
            statusCode: 400,
            body: JSON.stringify({ message: "El ID del estado es requerido." }),
          };
        }
        const sql = "DELETE FROM tb_estado WHERE id_estado = ?";
        await query(sql, [id_estado]);
        return {
          statusCode: 200,
          body: JSON.stringify({ message: 'Se eliminó el valor.' }),
        };
      }

      default: {
        return {
          statusCode: 405,
          body: JSON.stringify({ message: 'Método no permitido.' }),
        };
      }
    }
  } catch (err) {
    return {
      statusCode: 500,
      body: JSON.stringify({ message: 'Error: ' + err.message }),
    };
  }
};
  ```

### 3. Crea una API en API Gateway con los métodos GET, POST, PUT, y DELETE.

  - Asegúrate de activar la opción Lambda Proxy Integration en cada método
    
    ![Captura de pantalla 2024-08-11 163750](https://github.com/user-attachments/assets/ae5f3258-ea6d-49d5-bbf3-e078da6236f6)


  - Habilita CORS
    
    ![Captura de pantalla 2024-08-11 163414](https://github.com/user-attachments/assets/ebc54174-3e35-4ab8-9f10-3be108802104)


    ![image](https://github.com/user-attachments/assets/af4bd724-25f7-4568-9a8a-a3e9e058b97d)


### 4. Pruebas con POSTMAN

  - POST REQUEST:
    ![image](https://github.com/user-attachments/assets/d5b8aea2-e56d-462f-add3-9693767a3816)

  - GET REQUEST:
    ![image](https://github.com/user-attachments/assets/21839c1e-ba53-4b17-8251-ef084e747596)

  - PUT REQUEST:
    ![image](https://github.com/user-attachments/assets/52dc7bca-b8ab-435c-94e2-ec45b5e0d53b)

  - DELETE REQUEST:
    ![image](https://github.com/user-attachments/assets/1864b5f7-97a5-425c-95e7-e7c83ba0fa7c)



    

    
   
