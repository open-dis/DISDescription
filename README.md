# DISDescription

Central repository for machine-readable descriptions of the DIS protocol.

The files in this directory have been used with the xmlpg tool to auto-generate the source code found in the following github repositories; open-dis-java, open-dis-cpp, open-dis-python, open-dis-javascript.
A number of fixes, enhancements and other changes have been made directly to the source code each language over the years.
And so at this point the umbilical cord has been cut between these schema files and the language implementations.
Corrections are welcome to the schemas here, but please do not expect them to be automatically applied to the language repositories.
Re-generating the source code from these schemas and re-applying the changes to those language repositories would be a messy job.
Ideas are welcome, but at this point these schemas exist here for historical purposes only.

The repository has a very general name of "DISDescriptions" to leave open the possibility of using a format other than XML, such as JSON or ASN.1.

## DIS PDUs

The DIS Protocol Data Units (PDUs) are binary messages with a defined format.
For example the Entity State PDU has the Entity ID field 12 bytes from the start, and consists of three unsigned short values: site, application, and entity.
The combination of these three values together uniquely identifies an entity in the virtual world.

The IEEE DIS standard describes the PDUs in prose rather than in a machine-readable format.
The files in this directory describe the format of the PDUs in a way that can be read by a computer and used to generate a language implementation.
For example this fragment of XML:
~~~~
<class name="EntityID" inheritsFrom="root" comment="more laconically named EntityIdentifier">

  <attribute name="siteID" comment="Site ID">
    <primitive type= "unsigned short"/>
  </attribute>
  
  <attribute name="applicationID" comment="application number ID">
    <primitive type= "unsigned short"/>
  </attribute>
  
  <attribute name="entityID" comment="Entity number ID">
    <primitive type= "unsigned short"/>
  </attribute>
  
</class>
~~~~
Can be read by a program and compiled into a lanaguage implementation like the below.
Note that we have enough information to marshal and unmarshall the PDU--to change it from a binary representation to a language representation, and from a Java object to a binary representation.

~~~~
package edu.nps.moves.dis7;

import java.util.*;
import java.io.*;
import edu.nps.moves.disenum.*;
import edu.nps.moves.disutil.*;


/**
 * more laconically named EntityIdentifier
 *
 * Copyright (c) 2008-2016, MOVES Institute, Naval Postgraduate School. All rights reserved.
 * This work is licensed under the BSD open source license, available at https://www.movesinstitute.org/licenses/bsd.html
 *
 * @author DMcG
 */
public class EntityID extends Object implements Serializable
{
   /** Site ID */
   protected int  siteID;

   /** application number ID */
   protected int  applicationID;

   /** Entity number ID */
   protected int  entityID;


/** Constructor */
 public EntityID()
 {
 }

public int getMarshalledSize()
{
   int marshalSize = 0; 

   marshalSize = marshalSize + 2;  // siteID
   marshalSize = marshalSize + 2;  // applicationID
   marshalSize = marshalSize + 2;  // entityID

   return marshalSize;
}


public void setSiteID(int pSiteID)
{ siteID = pSiteID;
}

public int getSiteID()
{ return siteID; 
}

public void setApplicationID(int pApplicationID)
{ applicationID = pApplicationID;
}

public int getApplicationID()
{ return applicationID; 
}

public void setEntityID(int pEntityID)
{ entityID = pEntityID;
}

public int getEntityID()
{ return entityID; 
}


public void marshal(DataOutputStream dos)
{
    try 
    {
       dos.writeShort( (short)siteID);
       dos.writeShort( (short)applicationID);
       dos.writeShort( (short)entityID);
    } // end try 
    catch(Exception e)
    { 
      System.out.println(e);}
    } // end of marshal method

public void unmarshal(DataInputStream dis)
{
    try 
    {
       siteID = (int)dis.readUnsignedShort();
       applicationID = (int)dis.readUnsignedShort();
       entityID = (int)dis.readUnsignedShort();
    } // end try 
   catch(Exception e)
    { 
      System.out.println(e); 
    }
 } // end of unmarshal method 

} // end of class
~~~~
