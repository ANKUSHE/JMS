# JMS
Java Message Service 

#code for receiver 
import javax.jms.Connection;
import javax.jms.ConnectionFactory;

import javax.jms.JMSException;
import javax.jms.MessageConsumer;
import javax.jms.MessageProducer;
import javax.jms.Queue;
import javax.jms.Session;
import javax.jms.TextMessage;


import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.text.SimpleDateFormat;
import java.util.Calendar;

import org.apache.activemq.ActiveMQConnectionFactory;

public class Receive {
	
	final int i = 0;
    public static void main(String[] args) {
        try {
            // Create a ConnectionFactory
            ConnectionFactory connectionFactory = new ActiveMQConnectionFactory("tcp://localhost:61616");

            // Create a Connection
            Connection connection = connectionFactory.createConnection();
            connection.start();

            // Create a Session
            Session session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);

            // Create a Queue (if it doesn't exist already)
            Queue queue = session.createQueue("QueueName"); // Queue name (same as in producer)

            // Create a MessageConsumer
            MessageConsumer consumer = session.createConsumer(queue);
            
            Queue ackQueue = session.createQueue("ackQueue");
            
            MessageProducer producer = session.createProducer(ackQueue);
            TextMessage ackmsg = session.createTextMessage("ACK");
            
            
            
            

            // Set up a message listener
            consumer.setMessageListener(message -> {
                if (message instanceof TextMessage) {
                    try {
                    	Calendar calendar = Calendar.getInstance();
                    	SimpleDateFormat sdf = new SimpleDateFormat("dd:MMM:yyyy:HH:mm:ss");
                    	String formattedDate = sdf.format(calendar.getTime());
                        System.out.println("Received message at " + formattedDate + "  :- 	" + ((TextMessage) message).getText());
                        Thread.sleep(1000);
                        
                        String data1 = formattedDate + ":\n	" + ((TextMessage) message).getText();
                        
                        append(data1);
                                      
                        producer.send(ackmsg);
                    } catch (JMSException e) {
                        e.printStackTrace();
                    } catch (Exception e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
                }
            });

            // Keep the program running
            Thread.sleep(100000);

            // Clean up resources
            consumer.close();
            session.close();
            connection.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void append(String str)
    {
    	 
    	  try (BufferedWriter writer = new BufferedWriter(new FileWriter("C:\\Users\\VINAYAK\\Desktop\\FILES_dd_mon_yyyy_hh_mm\\file_log.txt",true))) {
              // Append data1 to the file
              writer.write( str );
              //writer.append(data1 );
              writer.newLine(); // Add a newline character after each line

            
          } catch (IOException e) {
              e.printStackTrace();
          }
          
    	
    }
    
    
    

}


