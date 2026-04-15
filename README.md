# EXAMEN
# EXAMEN
//descargar jar
https://sourceforge.net/projects/jdbcsql/

//
import java.awt.Color;
import java.awt.Component;
import java.sql.*;
import javax.swing.JOptionPane;
import javax.swing.JTable;
import javax.swing.table.DefaultTableCellRenderer;
import javax.swing.table.DefaultTableModel;
import pruebas.Conexion;


public class conectar extends javax.swing.JFrame {

    
    public conectar() {
        initComponents();
        LlenarTabla();
        
    }
    public void LlenarTabla() {
        String notas[] = {"NOTA"};
        String datos[] = new String[1]; // Solo una columna "nota"
        
        DefaultTableModel model = new DefaultTableModel(null, notas);
        jtablanotas.setModel(model);  // Asumimos que tu JTable se llama jtablanotas
        
        try {
            Conexion cc = new Conexion();
            Connection cn = cc.conectar();  // Método que devuelve la conexión

            String sql = "SELECT notas FROM prueba";  // Cambia "prueba" si tu tabla tiene otro nombre
            Statement psd = cn.createStatement();
            ResultSet rs = psd.executeQuery(sql);  // Ejecutar la consulta
            
            while (rs.next()) {
                datos[0] = rs.getString("notas");  // Obtener el valor de la columna 'notas'
                model.addRow(datos);  // Añadir una fila con los datos
            }

            // Llamar al método PintarTabla para aplicar el color a las celdas
            PintarTabla();

            // Cerrar la conexión
            cn.close();
            
        } catch (SQLException ex) {
            JOptionPane.showMessageDialog(null, ex);
        }
    }

    // Método para aplicar el formato condicional
    public void PintarTabla() {
        jtablanotas.setDefaultRenderer(Object.class, new DefaultTableCellRenderer() {
            public Component getTableCellRendererComponent(JTable t, 
                    Object v, boolean s, boolean f, int r, int c) {
                Component x = super.getTableCellRendererComponent(t, v, s, f, r, c);

                if (!s && v != null) {
                    // Convertir el valor de la celda a un número decimal
                    double n = Double.parseDouble(v.toString());

                    // Aplicar formato condicional:
                    // Si la nota es mayor a 7, ponemos el color verde
                    // Si es menor o igual a 7, ponemos el color rojo
                    if (n > 7) {
                        x.setBackground(Color.GREEN);  // Color verde
                    } else {
                        x.setBackground(Color.RED);  // Color rojo
                    }
                }

                return x;
            }
        });
    }
     private void jButton1ActionPerformed(java.awt.event.ActionEvent evt) {                                         
    Conexion cn = new Conexion();
    cn.conectar();
    }   

    //CONEXION
    public class Conexion  {
    Connection conectar;
    public Connection conectar(){
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            conectar =DriverManager.getConnection(
                    "jdbc:mysql://localhost/notas",
                    "root",
                    ""
            );
            JOptionPane.showMessageDialog(null, "conectado");
        } catch (Exception ex) {
          JOptionPane.showMessageDialog(null, ex);
        }
    return conectar;
}
}

esto es para sql 
INSERT INTO prueba (notas) VALUES (8.5);
INSERT INTO prueba (notas) VALUES (7.0);
INSERT INTO prueba (notas) VALUES (6.3);
INSERT INTO prueba (notas) VALUES (9.1);


ESTO ES DEL COMBO *******
package pruebas_Combobox;
import java.awt.Color;
import java.awt.Component;
import java.sql.*;
import javax.swing.DefaultComboBoxModel;
import javax.swing.DefaultListCellRenderer;
import javax.swing.JLabel;
import javax.swing.JList;
import javax.swing.JOptionPane;
import pruebas.Conexion;


public class combomio extends javax.swing.JFrame {
    
    // Usamos el modelo de comboBox globalmente
    DefaultComboBoxModel<String> model = new DefaultComboBoxModel<>();

    public combomio() {
        initComponents();
        // Llamar al método para llenar el combo cuando la ventana se inicializa
        LlenarComboBox();
        PintarCombo();  
    }
  public void LlenarComboBox() {
    jcombonota.setModel(model);  // Asignar el modelo al JComboBox

    try {
        Conexion cc = new Conexion();
        Connection cn = cc.conectar();  // Obtener la conexión

        String sql = "SELECT notas FROM prueba";  // Consulta SQL
        Statement psd = cn.createStatement();
        ResultSet rs = psd.executeQuery(sql);  // Ejecutar la consulta

        // Vaciar el modelo para evitar duplicados si se vuelve a llenar
        model.removeAllElements();

        while (rs.next()) {
            String notas = rs.getString("notas");  // Obtener el valor de la columna 'notas'

            // Verifica si el valor no es null y agrega un valor predeterminado si es necesario
            if (notas != null && !notas.isEmpty()) {
                model.addElement(notas);  // Agregar la nota como String al JComboBox
            } else {
                model.addElement("Valor no disponible");  // Valor predeterminado para valores nulos
            }
        }

        // Cerrar la conexión
        cn.close();

    } catch (SQLException ex) {
        JOptionPane.showMessageDialog(null, "Error de conexión o consulta: " + ex.getMessage());
    }
}
    // Método para pintar el JComboBox con formato condicional
 public void PintarCombo() {
    // Establecemos un renderizador personalizado para el JComboBox
    jcombonota.setRenderer(new DefaultListCellRenderer() {
        public Component getListCellRendererComponent(JList<?> list, Object value, int index, boolean isSelected, boolean cellHasFocus) {
            JLabel label = (JLabel) super.getListCellRendererComponent(list, value, index, isSelected, cellHasFocus);

            // Verificamos si el valor no es null y es un número
            if (value != null) {
                try {
                    // Convertir el valor a un número
                    double nota = Double.parseDouble(value.toString());

                    // Aplicar formato condicional basado en el valor numérico
                    if (nota > 7) {
                        label.setBackground(Color.GREEN);  // Color verde si es mayor a 7
                    } else {
                        label.setBackground(Color.RED);  // Color rojo si es menor o igual a 7
                    }
                } catch (NumberFormatException e) {
                    label.setBackground(Color.WHITE);  // Si no es un número, color por defecto
                }
            }

            // Eliminar el color celeste de selección: No cambiar el fondo cuando el ítem está seleccionado
            // No aplicamos ningún cambio en el fondo de la celda seleccionada

            return label;
        }
    });
 }
    
