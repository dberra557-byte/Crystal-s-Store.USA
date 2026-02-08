# Crystal-s-Store.USA
An online demo store using FakestoreAPI.
# Crystals. USA Store

A **demo online store** built with **HTML, JavaScript, Axios, and Bootstrap**.  
This project fetches products from [FakeStoreAPI](https://fakestoreapi.com/) and demonstrates CRUD functionality for educational purposes.

---

## Features

- **Home Page:** Welcome message with navigation.  
- **Product Listing:** Fetches and displays products in a responsive grid.  
- **Product Details:** View full product info with Edit, Delete, and Back buttons.  
- **Add Product:** Create new products via a form (simulated API).  
- **Edit Product:** Update existing product info (simulated API).  
- **Delete Product:** Confirmation before deleting products (simulated API).  
- **Responsive Design:** Works on desktop and mobile using Bootstrap.  
- **API Handling:** Shows loading indicators and error messages.

> ⚠️ **Note:** This project uses FakeStoreAPI. Changes (add/edit/delete) are simulated and do not persist.

---

## How to Run

1. Download or clone this repository.
2. Open the `index.html` file in any modern browser (Chrome, Edge, Firefox, Safari).  
3. Navigate the store using the navbar or buttons.

No installation required. Works completely offline in the browser.

---

## Technologies Used

- HTML5
- CSS3 + Bootstrap 5
- JavaScript (ES6)
- [Axios](https://axios-http.com/) for API requests
- [FakeStoreAPI](https://fakestoreapi.com/)

---

## Screenshots

*(Optional: Add screenshots here for GitHub README if you like)*

---

## Author

Your Name — Student Project

On Sat, Feb 7, 2026, 7:47 PM Diana Berra <diannamberra@gmail.com> wrote:
import React, { useEffect, useState } from 'react';
import { Routes, Route, Link, useNavigate, useParams } from 'react-router-dom';
import axios from 'axios';
import { Navbar, Nav, Container, Card, Button, Row, Col, Spinner, Alert, Form, Modal } from 'react-bootstrap';

export default function App() {
  return (
    <>
      <AppNavbar />
      <Container>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/products" element={<ProductList />} />
          <Route path="/products/:id" element={<ProductDetails />} />
          <Route path="/add-product" element={<AddProduct />} />
          <Route path="/edit-product/:id" element={<EditProduct />} />
        </Routes>
      </Container>
    </>
  );
}

// -------------------- Navbar --------------------
const AppNavbar = () => (
  <Navbar bg="dark" variant="dark" expand="lg" className="mb-4">
    <Container>
      <Navbar.Brand as={Link} to="/">FakeStore</Navbar.Brand>
      <Navbar.Toggle aria-controls="basic-navbar-nav" />
      <Navbar.Collapse id="basic-navbar-nav">
        <Nav className="ms-auto">
          <Nav.Link as={Link} to="/">Home</Nav.Link>
          <Nav.Link as={Link} to="/products">Products</Nav.Link>
          <Nav.Link as={Link} to="/add-product">Add Product</Nav.Link>
        </Nav>
      </Navbar.Collapse>
    </Container>
  </Navbar>
);

// -------------------- Home --------------------
const Home = () => {
  const navigate = useNavigate();
  return (
    <div className="text-center mt-5">
      <h1>Welcome to FakeStore</h1>
      <p>Your one-stop shop for testing product features with React!</p>
      <Button variant="primary" onClick={() => navigate('/products')}>
        View Products
      </Button>
    </div>
  );
};

// -------------------- ProductCard --------------------
const ProductCard = ({ product }) => {
  const navigate = useNavigate();
  return (
    <Card className="h-100">
      <Card.Img variant="top" src={product.image} style={{ height: '200px', objectFit: 'contain' }} />
      <Card.Body className="d-flex flex-column">
        <Card.Title>{product.title}</Card.Title>
        <Card.Text>${product.price}</Card.Text>
        <Button variant="primary" onClick={() => navigate(`/products/${product.id}`)}>
          View Details
        </Button>
      </Card.Body>
    </Card>
  );
};

// -------------------- Product List --------------------
const ProductList = () => {
  const [products, setProducts] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');

  useEffect(() => {
    axios.get('https://fakestoreapi.com/products')
      .then(res => setProducts(res.data))
      .catch(() => setError('Failed to load products'))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <Spinner animation="border" className="d-block mx-auto mt-5" />;
  if (error) return <Alert variant="danger" className="mt-5">{error}</Alert>;

  return (
    <Row xs={1} sm={2} md={3} lg={4} className="g-4">
      {products.map(product => (
        <Col key={product.id}>
          <ProductCard product={product} />
        </Col>
      ))}
    </Row>
  );
};

// -------------------- Product Details --------------------
const ProductDetails = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const [product, setProduct] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState('');
  const [showModal, setShowModal] = useState(false);

  useEffect(() => {
    axios.get(`https://fakestoreapi.com/products/${id}`)
      .then(res => setProduct(res.data))
      .catch(() => setError('Failed to load product'))
      .finally(() => setLoading(false));
  }, [id]);

  const handleDelete = () => {
    axios.delete(`https://fakestoreapi.com/products/${id}`)
      .then(() => navigate('/products'))
      .catch(() => setError('Failed to delete product'));
  };

  if (loading) return <Spinner animation="border" className="d-block mx-auto mt-5" />;
  if (error) return <Alert variant="danger" className="mt-5">{error}</Alert>;
  if (!product) return null;

  return (
    <>
      <Card className="text-center">
        <Card.Img variant="top" src={product.image} style={{ height: '300px', objectFit: 'contain' }} />
        <Card.Body>
          <Card.Title>{product.title}</Card.Title>
          <Card.Text>{product.description}</Card.Text>
          <Card.Text><strong>Category:</strong> {product.category}</Card.Text>
          <Card.Text><strong>Price:</strong> ${product.price}</Card.Text>
          <div className="d-flex justify-content-center gap-2">
            <Button variant="success" as={Link} to={`/edit-product/${product.id}`}>Edit Product</Button>
            <Button variant="danger" onClick={() => setShowModal(true)}>Delete Product</Button>
          </div>
        </Card.Body>
      </Card>

      <Modal show={showModal} onHide={() => setShowModal(false)} centered>
        <Modal.Header closeButton>
          <Modal.Title>Confirm Deletion</Modal.Title>
        </Modal.Header>
        <Modal.Body>Are you sure you want to delete this product?</Modal.Body>
        <Modal.Footer>
          <Button variant="secondary" onClick={() => setShowModal(false)}>Cancel</Button>
          <Button variant="danger" onClick={handleDelete}>Delete</Button>
        </Modal.Footer>
      </Modal>
    </>
  );
};

// -------------------- Add Product --------------------
const AddProduct = () => {
  const [formData, setFormData] = useState({ title: '', price: '', description: '', category: '' });
  const [message, setMessage] = useState('');

  const handleChange = e => setFormData({ ...formData, [e.target.name]: e.target.value });

  const handleSubmit = e => {
    e.preventDefault();
    axios.post('https://fakestoreapi.com/products', formData)
      .then(() => setMessage('Product successfully created!'))
      .catch(() => setMessage('Failed to create product'));
  };

  return (
    <div className="mt-4">
      <h2>Add Product</h2>
      {message && <Alert variant="info">{message}</Alert>}
      <Form onSubmit={handleSubmit}>
        <Form.Group className="mb-3">
          <Form.Label>Title</Form.Label>
          <Form.Control name="title" value={formData.title} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Price</Form.Label>
          <Form.Control type="number" name="price" value={formData.price} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Description</Form.Label>
          <Form.Control as="textarea" name="description" value={formData.description} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Category</Form.Label>
          <Form.Control name="category" value={formData.category} onChange={handleChange} required />
        </Form.Group>
        <Button variant="primary" type="submit">Create Product</Button>
      </Form>
    </div>
  );
};

// -------------------- Edit Product --------------------
const EditProduct = () => {
  const { id } = useParams();
  const [formData, setFormData] = useState({ title: '', price: '', description: '', category: '' });
  const [message, setMessage] = useState('');
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    axios.get(`https://fakestoreapi.com/products/${id}`)
      .then(res => setFormData(res.data))
      .catch(() => setMessage('Failed to load product'))
      .finally(() => setLoading(false));
  }, [id]);

  const handleChange = e => setFormData({ ...formData, [e.target.name]: e.target.value });

  const handleSubmit = e => {
    e.preventDefault();
    axios.put(`https://fakestoreapi.com/products/${id}`, formData)
      .then(() => setMessage('Product updated successfully!'))
      .catch(() => setMessage('Failed to update product'));
  };

  if (loading) return <Spinner animation="border" className="d-block mx-auto mt-5" />;

  return (
    <div className="mt-4">
      <h2>Edit Product</h2>
      {message && <Alert variant="info">{message}</Alert>}
      <Form onSubmit={handleSubmit}>
        <Form.Group className="mb-3">
          <Form.Label>Title</Form.Label>
          <Form.Control name="title" value={formData.title} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Price</Form.Label>
          <Form.Control type="number" name="price" value={formData.price} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Description</Form.Label>
          <Form.Control as="textarea" name="description" value={formData.description} onChange={handleChange} required />
        </Form.Group>
        <Form.Group className="mb-3">
          <Form.Label>Category</Form.Label>
          <Form.Control name="category" value={formData.category} onChange={handleChange} required />
        </Form.Group>
        <Button variant="primary" type="submit">Update Product</Button>
      </Form>
    </div>
  );
};
