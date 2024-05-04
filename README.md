# vendor-management-system-with-performance-metrics
# models.py
from django.db import models
from django.contrib.auth.models import User

class Vendor(models.Model):
    name = models.CharField(max_length=100)
    description = models.TextField(blank=True)

    def __str__(self):
        return self.name

class PerformanceMetric(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class VendorPerformance(models.Model):
    vendor = models.ForeignKey(Vendor, on_delete=models.CASCADE)
    metric = models.ForeignKey(PerformanceMetric, on_delete=models.CASCADE)
    value = models.DecimalField(max_digits=5, decimal_places=2)
    date_recorded = models.DateField(auto_now_add=True)

    def __str__(self):
        return f"{self.vendor.name} - {self.metric.name}: {self.value}"

# admin.py
from django.contrib import admin
from .models import Vendor, PerformanceMetric, VendorPerformance

admin.site.register(Vendor)
admin.site.register(PerformanceMetric)
admin.site.register(VendorPerformance)

# views.py
from django.shortcuts import render
from .models import Vendor, VendorPerformance

def vendor_list(request):
    vendors = Vendor.objects.all()
    return render(request, 'vendor_list.html', {'vendors': vendors})

def vendor_detail(request, pk):
    vendor = Vendor.objects.get(pk=pk)
    performances = VendorPerformance.objects.filter(vendor=vendor)
    return render(request, 'vendor_detail.html', {'vendor': vendor, 'performances': performances})

# urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.vendor_list, name='vendor_list'),
    path('vendor/<int:pk>/', views.vendor_detail, name='vendor_detail'),
]

# templates/vendor_list.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vendor List</title>
</head>
<body>
    <h1>Vendor List</h1>
    <ul>
        {% for vendor in vendors %}
            <li><a href="{% url 'vendor_detail' vendor.pk %}">{{ vendor.name }}</a></li>
        {% endfor %}
    </ul>
</body>
</html>

# templates/vendor_detail.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vendor Detail</title>
</head>
<body>
    <h1>Vendor: {{ vendor.name }}</h1>
    <p>Description: {{ vendor.description }}</p>
    <h2>Performance Metrics</h2>
    <ul>
        {% for performance in performances %}
            <li>{{ performance.metric.name }}: {{ performance.value }}</li>
        {% endfor %}
    </ul>
</body>
</html>
```

This is a basic setup that allows you to manage vendors, performance metrics, and their associated performance data. You'll need to create the templates and CSS stylesheets to style the UI according to your requirements. Additionally, you can expand this system by adding more features like user authentication, form validation, and data visualization.
