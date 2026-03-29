Preciso criar um sistema de gerenciamento de projetos para uso interno da empresa. Hoje a gente usa planilha no Google Sheets pra controlar tudo — projetos, tarefas, quem tá responsável, prazos — e tá insustentável. Ninguém sabe o status real das coisas, as pessoas esquecem de atualizar, e toda segunda-feira a gente perde 1 hora de reunião só pra alinhar o que cada um tá fazendo.

A empresa tem uns 40 funcionários, divididos em 6 times. Cada time toca entre 3 e 5 projetos ao mesmo tempo. O problema principal é visibilidade: o gestor não consegue ver de forma rápida o que tá atrasado, o que tá bloqueado e onde precisa intervir. Os próprios devs reclamam que não sabem a prioridade das coisas.

A ideia é ter um sistema web onde cada time tenha seus projetos, cada projeto tenha suas tarefas organizadas em colunas tipo kanban, e que tenha uma visão de dashboard pro gestor acompanhar tudo. Precisa ter controle de acesso — nem todo mundo pode ver todos os projetos, tem coisa de cliente que é confidencial.

Quero que tenha notificações quando alguém for atribuído a uma tarefa ou quando um prazo estiver chegando. E seria bom ter algum tipo de relatório semanal automático, pra matar aquela reunião de status.

A gente usa Slack pra comunicação e o time de dev trabalha com GitHub. Se der pra integrar com esses dois, já resolve 80% da fricção. O time de infra consegue subir o que for preciso, a gente já tem um cluster Kubernetes rodando outras coisas internas.

Não quero nada muito complexo. Não precisa de Gantt, nem de gestão de orçamento, nem de timesheet. O foco é simplicidade: a pessoa abre, vê o que tem pra fazer, arrasta o card, e segue. Se ficar complicado demais ninguém vai usar, igual já aconteceu quando tentaram implantar o Jira aqui.
