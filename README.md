### Descripción del Pipeline Jenkins

Este *pipeline* de Jenkins está diseñado para automatizar el ciclo de integración continua (CI) de una aplicación. A continuación, se detallan sus componentes y funciones principales:

#### Estructura General

```groovy
pipeline {
    agent any
```

* **`agent any`**: Indica que el pipeline puede ejecutarse en cualquier agente disponible en Jenkins.

---

### Stages (Etapas)

#### 1. **Source**

```groovy
stage('Source') {
    steps {
        git 'https://github.com/dantabarez/unir-cicd.git'
    }
}
```

* Clona el repositorio del proyecto desde GitHub.

#### 2. **Build**

```groovy
stage('Build') {
    steps {
        echo 'Building stage!'
        sh 'make build'
    }
}
```

* Compila el proyecto o construye las imágenes Docker mediante el comando `make build`.

#### 3. **Unit tests**

```groovy
stage('Unit tests') {
    steps {
        sh 'make test-unit'
        archiveArtifacts artifacts: 'results/unit_result.xml'
    }
}
```

* Ejecuta las pruebas unitarias.
* Guarda los resultados en `results/unit_result.xml`.

#### 4. **API tests**

```groovy
stage('API tests') {
    steps {
        sh 'make test-api'
        archiveArtifacts artifacts: 'results/api_result.xml'
    }
}
```

* Ejecuta pruebas de API para validar endpoints.
* Archiva los resultados.

#### 5. **E2E tests**

```groovy
stage('E2E tests') {
    steps {
        sh 'make test-e2e'
        archiveArtifacts artifacts: 'results/cypress_result.xml'
    }
}
```

* Ejecuta pruebas de extremo a extremo (E2E) usando Cypress.
* Guarda los resultados para revisión posterior.

---

### Post-Ejecución

```groovy
post {
    always {
        junit 'results/*_result.xml,results/cypress_result.xml'
        cleanWs()
    }
```

* **`always`**: Se ejecuta sin importar el resultado del pipeline.

  * Publica los reportes de pruebas (`JUnit`).
  * Limpia el espacio de trabajo para evitar residuos.

```groovy
    failure {
        mail to: 'dan.tabarez@gmail.com',
             subject: "Fallo en el pipeline: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
             body: "El pipeline '${env.JOB_NAME}' con el número de ejecución ${env.BUILD_NUMBER} ha fallado. Revisa los logs y reportes adjuntos en Jenkins."
    }
}
```

* **`failure`**: En caso de error, envía un correo con detalles del fallo y un enlace a los reportes generados.

---

### Conclusión

Este *pipeline* implementa buenas prácticas de CI: clonado de código, construcción automatizada, pruebas en distintos niveles (unitarias, API, E2E), reporte de resultados y notificación ante fallos. Facilita la entrega continua al detectar errores temprano en el ciclo de desarrollo.
