## Aula 26 - Listando agenda do prestador

- Mostar no painel do prestador de serviço a listagem de sua agenda
- Criar uma nova rota para agenda do provider (schedule)
- Criar um novo controller: ScheduleController.js
- Verificar se o usuário logado é um provider(prestador)
- buscar agenda pela data e fazer um parseISO
- buscar os agendamentos do provedor logado, que não esteja cancelado e que a data seja no ínicio do dia buscado até o final do dia.

```
import { startOfDay, endOfDay, parseISO } from 'date-fns';
import { Op } from 'sequelize';
import Appointment from '../models/Appointment';
import User from '../models/User';

class ScheduleController {
  async index(req, res) {
    const checkUserProvider = await User.findOne({
      where: {
        id: req.userId,
        provider: true,
      },
    });

    if (!checkUserProvider) {
      return res.status(401).json({ error: 'User is not a provider' });
    }

    const { date } = req.query;
    const parsedDate = parseISO(date);

    const appointments = await Appointment.findAll({
      where: {
        provider_id: req.userId,
        canceled_at: null,
        date: {
          [Op.between]: [startOfDay(parsedDate), endOfDay(parsedDate)],
        },
      },
      order: ['date'],
    });
    return res.json(appointments);
  }
}

export default new ScheduleController();
```
Fim: [https://github.com/tgmarinho/gobarber/tree/aula26](https://github.com/tgmarinho/gobarber/tree/aula26)
