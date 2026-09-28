// SISTEMA DE GESTIÓN DE TRANSPORTE URBANO EN EL MUNICPIO DE TECNOVALLE

import java.time.Duration;
import java.time.LocalDateTime;
import java.util.Comparator;
import java.util.List;
import java.util.Map;
import java.util.function.Function;
import java.util.function.Predicate;
import java.util.stream.Collectors;

public class SistemaTransporte {

    static final class RegistroTransporte {
        private final int idUsuario;
        private final String ruta;
        private final String estacion;
        private final String accion;
        private final LocalDateTime timestamp;

        RegistroTransporte(int idUsuario, String ruta, String estacion,
                           String accion, LocalDateTime timestamp) {
            this.idUsuario = idUsuario;
            this.ruta = ruta;
            this.estacion = estacion;
            this.accion = accion;
            this.timestamp = timestamp;
        }

        int getIdUsuario() { return idUsuario; }
        String getRuta() { return ruta; }
        String getEstacion() { return estacion; }
        String getAccion() { return accion; }
        LocalDateTime getTimestamp() { return timestamp; }
    }

    // Funcion pura: filtra sin cambiar la lista original.
    static List<RegistroTransporte> filtrar(
            List<RegistroTransporte> registros,
            Predicate<RegistroTransporte> condicion) {
        return registros.stream().filter(condicion).collect(Collectors.toList());
    }

    // Funcion de orden superior: recibe una funcion para transformar datos.
    static <T> List<T> transformar(
            List<RegistroTransporte> registros,
            Function<RegistroTransporte, T> funcion) {
        return registros.stream().map(funcion).collect(Collectors.toList());
    }

    static Map<String, Long> afluenciaPorEstacion(List<RegistroTransporte> registros) {
        return filtrar(registros, r -> r.getAccion().equals("entrada"))
                .stream()
                .collect(Collectors.groupingBy(
                        RegistroTransporte::getEstacion,
                        Collectors.counting()));
    }

    static Map<Integer, Long> horasPico(List<RegistroTransporte> registros) {
        return filtrar(registros, r -> r.getAccion().equals("entrada"))
                .stream()
                .collect(Collectors.groupingBy(
                        r -> r.getTimestamp().getHour(),
                        Collectors.counting()));
    }

    static Map<String, Long> rutasMasUtilizadas(List<RegistroTransporte> registros) {
        return filtrar(registros, r -> r.getAccion().equals("entrada"))
                .stream()
                .collect(Collectors.groupingBy(
                        RegistroTransporte::getRuta,
                        Collectors.counting()));
    }

    static Map<Integer, List<String>> patronesPorUsuario(
            List<RegistroTransporte> registros) {
        return registros.stream()
                .sorted(Comparator.comparing(RegistroTransporte::getTimestamp))
                .collect(Collectors.groupingBy(
                        RegistroTransporte::getIdUsuario,
                        Collectors.mapping(
                                RegistroTransporte::getEstacion,
                                Collectors.toList())));
    }

    static double tiempoPromedioEntreEstaciones(
            List<RegistroTransporte> registros) {
        Map<Integer, List<RegistroTransporte>> porUsuario = registros.stream()
                .sorted(Comparator.comparing(RegistroTransporte::getTimestamp))
                .collect(Collectors.groupingBy(RegistroTransporte::getIdUsuario));

        List<Long> minutos = porUsuario.values().stream()
                .flatMap(lista -> {
                    List<RegistroTransporte> ordenada = lista.stream()
                            .sorted(Comparator.comparing(
                                    RegistroTransporte::getTimestamp))
                            .collect(Collectors.toList());

                    return java.util.stream.IntStream.range(1, ordenada.size())
                            .mapToObj(i -> Duration.between(
                                    ordenada.get(i - 1).getTimestamp(),
                                    ordenada.get(i).getTimestamp()).toMinutes());
                })
                .collect(Collectors.toList());

        return minutos.stream().mapToLong(Long::longValue).average().orElse(0.0);
    }

    static Map<String, String> detectarRutasCriticas(
            List<RegistroTransporte> registros, long umbral) {
        return rutasMasUtilizadas(registros).entrySet().stream()
                .collect(Collectors.toMap(
                        Map.Entry::getKey,
                        e -> e.getValue() >= umbral ? "critica" : "normal"));
    }

    public static void main(String[] args) {
        // Escenario simulado del Municipio de Tecno Valle.
        // Los identificadores y codigos de ruta se mantienen en pares.
        final List<RegistroTransporte> registros = List.of(
                new RegistroTransporte(202, "R2", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 6, 20)),
                new RegistroTransporte(202, "R2", "San Javier", "salida", LocalDateTime.of(2026, 9, 20, 6, 40)),
                new RegistroTransporte(204, "R4", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 6, 40)),
                new RegistroTransporte(204, "R4", "Alpujarra", "salida", LocalDateTime.of(2026, 9, 20, 7, 0)),
                new RegistroTransporte(206, "R6", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 7, 20)),
                new RegistroTransporte(206, "R6", "Parque Berrío", "salida", LocalDateTime.of(2026, 9, 20, 7, 40)),
                new RegistroTransporte(208, "R8", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 8, 0)),
                new RegistroTransporte(208, "R8", "Industriales", "salida", LocalDateTime.of(2026, 9, 20, 8, 20)),
                new RegistroTransporte(210, "R2", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 8, 20)),
                new RegistroTransporte(210, "R2", "San Javier", "salida", LocalDateTime.of(2026, 9, 20, 8, 40)),
                new RegistroTransporte(212, "R4", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 8, 40)),
                new RegistroTransporte(212, "R4", "Alpujarra", "salida", LocalDateTime.of(2026, 9, 20, 9, 0)),
                new RegistroTransporte(214, "R6", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 9, 0)),
                new RegistroTransporte(214, "R6", "Parque Berrío", "salida", LocalDateTime.of(2026, 9, 20, 9, 20)),
                new RegistroTransporte(216, "R8", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 9, 20)),
                new RegistroTransporte(216, "R8", "Industriales", "salida", LocalDateTime.of(2026, 9, 20, 9, 40)),
                new RegistroTransporte(218, "R2", "San Javier", "entrada", LocalDateTime.of(2026, 9, 20, 10, 0)),
                new RegistroTransporte(218, "R2", "San Antonio", "salida", LocalDateTime.of(2026, 9, 20, 10, 20)),
                new RegistroTransporte(220, "R4", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 10, 20)),
                new RegistroTransporte(220, "R4", "Alpujarra", "salida", LocalDateTime.of(2026, 9, 20, 10, 40)),
                new RegistroTransporte(222, "R6", "San Antonio", "entrada", LocalDateTime.of(2026, 9, 20, 11, 0)),
                new RegistroTransporte(222, "R6", "Parque Berrío", "salida", LocalDateTime.of(2026, 9, 20, 11, 20)),
                new RegistroTransporte(224, "R2", "Poblado", "entrada", LocalDateTime.of(2026, 9, 20, 12, 0)),
                new RegistroTransporte(224, "R2", "San Antonio", "salida", LocalDateTime.of(2026, 9, 20, 12, 20)),
                new RegistroTransporte(226, "R4", "Alpujarra", "entrada", LocalDateTime.of(2026, 9, 20, 7, 40)),
                new RegistroTransporte(226, "R4", "San Antonio", "salida", LocalDateTime.of(2026, 9, 20, 8, 0)),
                new RegistroTransporte(228, "R6", "Parque Berrío", "entrada", LocalDateTime.of(2026, 9, 20, 11, 20)),
                new RegistroTransporte(228, "R6", "San Antonio", "salida", LocalDateTime.of(2026, 9, 20, 11, 40))
        );

        System.out.println("RESULTADOS - MUNICIPIO DE TECNO VALLE");
        System.out.println();

        System.out.println("1. Afluencia por estacion");
        afluenciaPorEstacion(registros).entrySet().stream()
                .sorted(Map.Entry.comparingByKey())
                .forEach(e -> System.out.println(e.getKey() + ": "
                        + e.getValue() + " entradas"));

        System.out.println();
        System.out.println("2. Registros por hora");
        horasPico(registros).entrySet().stream()
                .sorted(Map.Entry.<Integer, Long>comparingByValue().reversed())
                .forEach(e -> System.out.println(e.getKey() + ":00 = "
                        + e.getValue() + " entradas"));

        System.out.println();
        System.out.println("3. Rutas mas utilizadas");
        rutasMasUtilizadas(registros).entrySet().stream()
                .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
                .forEach(e -> System.out.println(e.getKey() + ": "
                        + e.getValue() + " entradas"));

        System.out.println();
        System.out.println("4. Patrones de viaje por usuario");
        patronesPorUsuario(registros).entrySet().stream()
                .sorted(Map.Entry.comparingByKey())
                .forEach(e -> System.out.println("Usuario " + e.getKey()
                        + ": " + e.getValue()));

        System.out.println();
        System.out.println("5. Tiempo promedio entre estaciones");
        System.out.printf("%.2f minutos%n", tiempoPromedioEntreEstaciones(registros));

        System.out.println();
        System.out.println("6. Estado de las rutas");
        detectarRutasCriticas(registros, 4).entrySet().stream()
                .sorted(Map.Entry.comparingByKey())
                .forEach(e -> System.out.println(e.getKey()
                        + ": " + e.getValue()));

        // Composicion: una funcion obtiene la estacion y otra la pasa a mayusculas.
        Function<RegistroTransporte, String> obtenerEstacion =
                RegistroTransporte::getEstacion;
        Function<String, String> pasarAMayusculas =
                texto -> texto.toUpperCase();
        Function<RegistroTransporte, String> estacionNormalizada =
                obtenerEstacion.andThen(pasarAMayusculas);

        System.out.println();
        System.out.println("Ejemplo de composicion: "
                + transformar(registros, estacionNormalizada).get(0));
    }
}

