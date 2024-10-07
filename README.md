SQL CODE TO STORE SIM CARD INFORMATION

CREATE TABLE sim_cards ( sim_number VARCHAR(20) PRIMARY KEY, phone_number VARCHAR(15) UNIQUE NOT NULL, status ENUM('active', 'inactive') NOT NULL, activation_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP );

NOSQL MONGODB // Create collection db.createCollection("sim_cards");

// Insert a document db.sim_cards.insertOne({ "sim_number": "ABC123456789", "phone_number": "+1234567890", "status": "active", "activation_date": new Date("2024-10-07T12:34:56Z") });

CODE const express = require('express'); const mongoose = require('mongoose');

// Create an Express application const app = express(); app.use(express.json()); // Middleware to parse JSON request bodies

// MongoDB connection string (replace with your own connection string) const mongoURI = 'mongodb://localhost:27017/sim_service';

// Connect to MongoDB mongoose.connect(mongoURI, { useNewUrlParser: true, useUnifiedTopology: true, }) .then(() => { console.log('Connected to MongoDB'); }) .catch((error) => { console.error('Error connecting to MongoDB:', error); });

// Define the SIM card schema const simCardSchema = new mongoose.Schema({ sim_number: { type: String, unique: true, required: true }, phone_number: { type: String, unique: true, required: true }, status: { type: String, enum: ['active', 'inactive'], required: true }, activation_date: { type: Date }, });

const SimCard = mongoose.model('SimCard', simCardSchema);

// Helper function to validate input const validateInput = (req, res, next) => { const { sim_number } = req.body; if (!sim_number || typeof sim_number !== 'string') { return res.status(400).json({ message: 'Invalid or missing sim_number' }); } next(); };

// Activate SIM Card app.post('/activate', validateInput, async (req, res) => { const { sim_number } = req.body;

try { const simCard = await SimCard.findOne({ sim_number });

if (!simCard) {
  return res.status(404).json({ message: 'SIM card not found' });
}

if (simCard.status === 'active') {
  return res.status(400).json({ message: 'SIM card is already active' });
}

simCard.status = 'active';
simCard.activation_date = new Date();
await simCard.save();

res.status(200).json(simCard);
} catch (error) { res.status(500).json({ message: 'Error activating SIM card', error }); } });

// Deactivate SIM Card app.post('/deactivate', validateInput, async (req, res) => { const { sim_number } = req.body;

try { const simCard = await SimCard.findOne({ sim_number });

if (!simCard) {
  return res.status(404).json({ message: 'SIM card not found' });
}

if (simCard.stAtus === 'inactive') {
  return res.status(400).json({ message: 'SIM card is already inactive' });
}

simCard.status = 'inactive';
await simCard.save();

res.status(200).json(simCard);
} catch (error) { res.status(500).json({ message: 'Error deactivating SIM card', error }); } });

// Get SIM Details app.get('/sim-details/:simNumber', async (req, res) => { const { simNumber } = req.params;

try { const simCard = await SimCard.findOne({ sim_number: simNumber });

if (!simCard) {
  return res.status(404).json({ message: 'SIM card not found' });
}

res.status(200).json(simCard);
} catch (error) { res.status(500).json({ message: 'Error fetching SIM card details', error }); } });

// Start the server const PORT = process.env.PORT || 3000; app.listen(PORT, () => { console.log(Server is running on port ${PORT}); });

APPROACH FOR SOLVING THE PROBLEM The primary goal of the SIM Card Activation service is to provide an easy-to-use interface for managing SIM card activations.

API Endpoints: The service exposes endpoints for activating, checking the status, and deactivating SIM cards. Data Storage: This can be easily done with a database. Validation: Basic validation is implemented to ensure that SIM cards are only activated if they meet specific criteria (e.g., valid format, not already activated). Error Handling: The service includes error handling to return meaningful HTTP messages.
